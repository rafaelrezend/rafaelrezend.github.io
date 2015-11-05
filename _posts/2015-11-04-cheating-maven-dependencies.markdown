---
layout: post
title: Quick dirty trick&#58; Overruling Maven Transitive Dependency
---

In my example, I'm creating a Jenkins plugin (so, Maven) which has a dependency to the [Team Concert plugin](https://wiki.jenkins-ci.org/display/JENKINS/Team+Concert+Plugin) from IBM. As expected, Maven is going to resolve this dependency by fetching the corresponding .jar file from the Jenkins public repository, listed in the repository list of my own plugin.
So I've added the following to my plugin's pom file:

```
<dependencies>
    <dependency>
        <groupId>org.jenkins-ci.plugins</groupId>
        <artifactId>teamconcert</artifactId>
        <version>1.1.9.5</version>
    </dependency>
</dependencies>
```

And when executed...

```
[INFO] ------------------------------------------------------------------------
[INFO] Building TODO Plugin 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
Downloading: http://repo.jenkins-ci.org/public/com/ibm/team/build/com.ibm.team.build.hjplugin-rtc/1.0.2/com.ibm.team.build.hjplugin-rtc-1.0.2.pom
Downloading: http://repo.maven.apache.org/maven2/com/ibm/team/build/com.ibm.team.build.hjplugin-rtc/1.0.2/com.ibm.team.build.hjplugin-rtc-1.0.2.pom
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 16.221s
[INFO] Finished at: Fri Oct 09 19:00:33 CEST 2015
[INFO] Final Memory: 19M/249M
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal on project my-plugin: Could not resolve dependencies for project my-sample:my-plugin:hpi:1.0-SNAPSHOT: Failed to collect dependencies at org.jenkins-ci.plugins:teamconcert:jar:1.1.9.4 -> com.ibm.team.build:com.ibm.team.build.hjplugin-rtc:jar:1.0.2: Failed to read artifact descriptor for com.ibm.team.build:com.ibm.team.build.hjplugin-rtc:jar:1.0.2: Could not transfer artifact com.ibm.team.build:com.ibm.team.build.hjplugin-rtc:pom:1.0.2 from/to repo.jenkins-ci.org (http://repo.jenkins-ci.org/public/): repo.jenkins-ci.org: nodename nor servname provided, or not known: Unknown host repo.jenkins-ci.org: nodename nor servname provided, or not known -> [Help 1]
[ERROR] 
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR] 
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/DependencyResolutionException

```

Unluckily, *Team Concert plugin* has a dependency of its own ([com.ibm.team.build.hpiplugin-rtc](https://github.com/jenkinsci/teamconcert-plugin/tree/master/com.ibm.team.build.hjplugin-rtc)), which is resolved locally in the developers' environment (not nice, uhm IBM?). No **.jar** is provided anywhere, so the only solution would be to build the dependency ourselves and install it in the local maven. This would require a bulky package -- part of the *Rational Team Concert* API -- and an environment similar of those devs.

That sounds awkward, yes, especially because what I really need -- the **.jar** file of the *Team Concert plugin* -- is already there. But before we blame Maven, we can identify that the dependency between the *Team Concert plugin* and [com.ibm.team.build.hpiplugin-rtc](https://github.com/jenkinsci/teamconcert-plugin/tree/master/com.ibm.team.build.hjplugin-rtc) happens to be declared with the **default scope**, that is, **compile scope**.

From *[Maven: Introduction to the Dependency Mechanism](https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html)*:

> ***compile*** : This is the default scope, used if none is specified. Compile dependencies are available in all classpaths of a project. ***Furthermore, those dependencies are propagated to dependent projects.***

As a fact, I cannot change the dependency relation in the *Team Concert plugin*. Also, I'm not exactly willing to download and setup the IBM environment. My last resort is a dirty trick that, although very simple, I could not figure out at first without help:

As we know, when Maven resolves dependencies from an external repository, it's actually installing each of them on the local machine (*.m2* folder). This hidden folder is nicely managed by Maven. Until something goes really wrong, Maven users don't even need to know this folder exists.
Still, this is a repo with plenty of **.jar** files and their respective **.pom**, where the dependencies are listed. So, in order to cut a dependency from our environment, we will need a little twist in one of those *.pom* files.

*My plugin* &#8658; *Team Concert plugin* &#8658; *com.ibm.team.build.hpiplugin-rtc*

To proceed, I'll open the `org.jenkins-ci.plugins:teamconcert:jar:1.1.9.4` Maven file at `.m2/repository/org/jenkins-ci/plugins/teamconcert/1.1.9.4/teamconcert-1.1.9.4.pom` and comment the following dependency:

```
<dependencies>
    <!--
    <dependency>
        <groupId>com.ibm.team.build</groupId>
        <artifactId>com.ibm.team.build.hjplugin-rtc</artifactId>
        <version>1.0.2</version>
    </dependency>
    -->
    <dependency>
        <groupId>com.ibm.team.build</groupId>
        <artifactId>com.ibm.team.build.hjplugin-rtc</artifactId>
        <version>1.0.2</version>
        <type>test-jar</type>
        <scope>test</scope>
    </dependency>
...

</dependencies>
```

Building the project once again...

```
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 1:42.720s
[INFO] Finished at: Fri Oct 09 19:19:32 CEST 2015
[INFO] Final Memory: 38M/225M
[INFO] ------------------------------------------------------------------------
````

***Voilà!!!***