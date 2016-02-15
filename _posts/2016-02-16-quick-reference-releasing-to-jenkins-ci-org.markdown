---
layout: post
title: Quick reference&#58; Releasing to jenkins-ci.org
---

No questions. This is the ultimate guide on how to release a plugin to [jenkins-ci.org](http://jenkins-ci.org/):

* [Jenkins Wiki: Hosting Plugins](https://wiki.jenkins-ci.org/display/JENKINS/Hosting+Plugins)

---
But when I'm in the dev loop already, I simply want to skip those steps and go straight to what really matters: *release the plugin and update the documentation*.
And so, the short guide below is a note for myself. The commands are easy, but I wanted to keep them as a checklist :-)

Implementation is done and changes are committed.
Since I use **ssh** to communicate with GitHub, double-check if I have the right keys.

{% highlight shell %}
ssh -T git@github.com
{% endhighlight %}

If permission is denied, ops, my key is not there! Add it.

{% highlight shell %}
ssh-add {home}/.ssh/github_rsa
{% endhighlight %}

Why is it important? Authentication is not the first step. If it fails, there will be a little mess to clean up:<br>
[Clean up a failed release in the Jenkins public repository]({{ site.github.url }}/post/cleanup-failed-release-jenkins-public-repository)

Next, release.

{% highlight shell %}
mvn release:prepare release:perform
{% endhighlight %}

Since I'm forced to use **ssh** because of my mismatching credentials (GitHub and jenkins-ci.org servers), there is no need to provide user and password parameters in the command above.
Explanation on the Jenkins guide provided on top of this page.

At this point, a new tag has already been created in the plugin repository of GitHub [jenkinsci](https://github.com/jenkinsci) page with two commits:

{% highlight shell %}
[maven-release-plugin] prepare release <plugin-id-and-version≤>
[maven-release-plugin] prepare for next development iteration
{% endhighlight %}

The same commits will be created locally. So, simply push it to my own fork to keep it in sync:

{% highlight shell %}
git push origin master
{% endhighlight %}

Now, *This branch is even with jenkinsci:master.*

Finally, update the Jenkins Wiki page:

* Instructions (if applicable)
* Changelog (always!)

Done!