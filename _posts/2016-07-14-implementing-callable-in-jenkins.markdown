---
layout: post
title: Implementing Callable in a Jenkins plugin
---

I recently had the task to integrate an external library into a Jenkins plugin. It is straightforward, of course! But it becomes a bit of a problem when the library is handling I/O on its own, using ***java.io.File***.

As known, Jenkins provides a handy abstraction with **[FilePath](http://javadoc.jenkins-ci.org/?hudson/FilePath.html)** and **[Launcher](http://javadoc.jenkins-ci.org/?hudson/Launcher.html)** to resolve the node (master, slaves...) where the *Job* is running. For this reason, whenever one needs to execute a command line or write to a file, these methods are used without any concern about the Jenkins distribution.
If regular **java.io.File** is used instead, the build step will try to write directly into the Master node even when the *Job* has been allocated somewhere else.

### What if an external library is doing I/O with ***java.io.File***?

If so, we wrap the whole execution and send it to the correct node!

The wrapper is a **[Callable](http://javadoc.jenkins-ci.org/hudson/remoting/Callable.html)** class, conveniently provided through four self-descriptive extensions: **[MasterToSlaveCallable](http://javadoc.jenkins-ci.org/jenkins/security/MasterToSlaveCallable.html)**, **[SlaveToMasterCallable](http://javadoc.jenkins-ci.org/jenkins/security/SlaveToMasterCallable.html)**, **[MasterToSlaveFileCallable](http://javadoc.jenkins-ci.org/jenkins/MasterToSlaveFileCallable.html)** and **[SlaveToMasterFileCallable](http://javadoc.jenkins-ci.org/jenkins/SlaveToMasterFileCallable.html)**.

A **Callable** is sent through a **[Channel](http://javadoc.jenkins-ci.org/hudson/remoting/Channel.html)**, which is in turn **provided by the [launcher](http://javadoc.jenkins-ci.org/hudson/Launcher.html) of the builder plugin itself**.

## Example

Our Jenkins plugin uses the *[SimpleIOExample](https://github.com/rafaelrezend/jenkins-callable-example/tree/master/SimpleIOExample)* created for this example. This external library simply gets an input *message* and write it to a file.
The plugin uses this library to write whatever is provided via its *Build Step* in the Jenkins interface.

Our wrapper Callable -- **WriteToNode** -- is very uncomplicated.
The external library is only referenced within the *call()* method.

{% highlight java %}
private static class WriteToNode extends MasterToSlaveCallable<Void, IOException> {
	
	private String message;
	private FilePath outputFile;

	public WriteToNode(String message, FilePath outputFile) {
		this.message = message;
		this.outputFile = outputFile;
	}

	@Override
	public Void call() throws IOException {

		// The external libs are used inside the call,
		// so that they are executed within the selected node.
		// This class comes from an external library.
		SimpleIOExample sio = new SimpleIOExample();
		
		sio.writeToFile(
				"Distributed Jenkins: is it written in the correct node?\n"
				+ "Filepath: " + outputFile.getRemote() + "\n"
				+ "Your message: \n" + message + "\n",
				outputFile.getRemote());

		return null;
	}
}
{% endhighlight %}


Then, in the *perform()* method of our *Build Step*, the execution is resolved in a single line:

{% highlight java %}
launcher.getChannel().call(new WriteToNode(message, new FilePath(workspace, OUTPUT_FILE)));
{% endhighlight %}

To change the return type of the *call()* method, simply set the extension **MasterToSlaveCallable<TYPE, IOException>**.
Same for the *Exception* type.
For the complete implementation -- with comments -- inspect code [here](https://github.com/rafaelrezend/jenkins-callable-example/blob/master/jenkins-io-example/src/main/java/org/jenkinsci/plugins/jenkinsioexample/CallableIOBuilder.java).

For more details, check out [Making your plugin behave in distributed Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Making+your+plugin+behave+in+distributed+Jenkins).
Special mention for the **Performance consideration**: *FilePath* can be used to read data from the Slave filesystem for further processing in the Master node.
For bulky data, however, it might be more efficient to send the code to where the data is.
The solution in this post applies then.