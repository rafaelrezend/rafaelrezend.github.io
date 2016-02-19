---
layout: post
title: Clean up a failed release in the Jenkins public repository
---

When contributing a plugin to the [Jenkins public repository](https://github.com/jenkinsci), several things can go wrong and leave traces which are difficult to clean up. Wrong credentials, missing configuration and network instability are just a few of the issues we could face. A common (and suggested) practice -- for those who don't like to get the hands dirty with SCM stuff -- is to simply increment the plugin version, ignore the current one and move on with a new release ([Jenkins guide: Releasing to jenkinsci](https://wiki.jenkins-ci.org/display/JENKINS/Hosting+Plugins#HostingPlugins-Releasingtojenkinsci.org)).

As I like to keep things tight and clean, I prefer to follow another direction and rollback the whole process as if nothing has ever happened. As so, this short guide is a reminder -- *mostly to myself* -- on how to bring the GIT repository of my Jenkins plugin back to a clean state. Misusage of the commands below are potentially dangerous. Therefore, *use them carefully and at your own risk!*

To contextualize, the **upstream** repository is the `jenkinsci` and **origin** is my fork from it. Also, in this example I'm trying to release a plugin with *artifactId* `my-plugin` in its version `1.0.0`. So, the plugin's version in the current `pom.xml` file is `1.0.0-SNAPSHOT`.

First, **maven-release-plugin** will create a *tag* of your plugin in the repository. By default it will be `my-plugin-1.0.0`.
These are the commands to remove this tag from the *upstream*:

{% highlight shell %}
git tag -d play-autotest-plugin-1.0.0
git push upstream :play-autotest-plugin-1.0.0
{% endhighlight %}

Next, the release process will generate two additional commits to prepare the project for the next development cycle. These commits are shown on Github as follows:

**[maven-release-plugin] prepare for next development iteration**</br>
**[maven-release-plugin] prepare release my-plugin-1.0.0**

To undo them, we need to point the HEAD two commits behind:

{% highlight shell %}
git push -f upstream HEAD^^:master
{% endhighlight %}

Since we are executing the release command locally, the same commits will be present in our sandbox. To undo them, simply reset with the *origin*, which is still clean.

{% highlight shell %}
git reset --hard origin/master
{% endhighlight %}

Finally, two files are created locally as output, which should be removed before the next attempt to release the plugin:

{% highlight shell %}
rm pom.xml.releaseBackup release.properties
{% endhighlight %}

Now, everything is back to normal. Hope it's useful!