---
layout: post
title: Resolving Maven dependencies from a local p2 repository
---

***Warning!*** If you got here looking for a solution for your own project, **think twice, you might be doing it wrong!**

The beauty of Maven is, above all, its capabilities to solve dependencies seamlessly from remote artifact repositories. Give your project a URL, a list of plugins and you are good to go. Local dependency is nothing but a burden for your *dev* colleagues and the *Continuous Integration* environment.

* * *

HOWEVER,
let's say you are working with proprietary libraries from a company that insists to know all about you before you are ever allowed to download and use their API *(and don't forget the "Have a sales representative contact me" checkbox)*.
This API, you guessed, is not available in a Maven repository. Instead, it comes as a compressed p2 repository.

A practical example would be in the **Team Concert plugin**, more specifically the project [com.ibm.team.build.hjplugin-rtc](https://github.com/jenkinsci/teamconcert-plugin/tree/master/com.ibm.team.build.hjplugin-rtc) within it.

If you inspect its [pom.xml](https://raw.githubusercontent.com/jenkinsci/teamconcert-plugin/master/com.ibm.team.build.hjplugin-rtc/pom.xml), there are two parts that at least make our life easier by accepting parameters from command line:

- Property declaration
{% highlight xml %}
<rtc-client-p2repo-url>${env.RTC_Client_p2Repo}</rtc-client-p2repo-url>
{% endhighlight %}

- Repository declaration
{% highlight xml %}
<repository>
    <id>rtc-401</id>
    <layout>p2</layout>
    <url>${rtc-client-p2repo-url}</url>
</repository>
{% endhighlight %}


The Maven project above will compile only if a repository is provide via the `${env.RTC_Client_p2Repo}` property.
Once the p2 repository is decompressed in the local filesystem (i.e. *C:\RTC-Client-p2Repo-5.0.1* for Windows), you can compile it as follows:

{% highlight shell %}
mvn -Denv.RTC_Client_p2Repo=file:///C:\RTC-Client-p2Repo-5.0.1 clean compile
{% endhighlight %}

It's straightforward, indeed! But the trick is the `file:///` prefix that, if missed, won't be accepted as a `<url>`.

Done.