---
layout: post
title: Jenkins&#58; from freestyle to Workflow
---

[Jenkins](https://jenkins-ci.org/) was first released in February 2005 (then Hudson) as a server-based Continuous Integration platform and soon became the leading application in this domain. Its rapid growth and success is partly because Jenkins is open-source from conception. The extension mechanism allowed hundreds of developers to collaborate through small and independent plugins for multiple applications. To date, there are nearly 1010 publicly available contributions.

Another reason, from my humble point of view, is the shiny GUI that comes with this package, which most probably contributed to enchant those who preferred such *tangible* experience over plain-text Maven, Ant etc. Setting up a build has never been that easy.

Jenkins was designed so that every build configuration could be described in a separate self-contained *job*. Every job has, among other features, sequential build steps and post-build steps which are executed once a build is requested. In the meantime, further requests are queued, waiting for resources (aka *executors*) to become available once again.

This design regards the job as a single resource, assigned at the beginning of the build execution and held until the very end. In practice, though, every build and post-build step may require a different set of resources. Envisioning the job in a finer granularity automatically prompts the idea that, in fact, multiple build requests could be handled concurrently with the same set of atomic resources. As soon as a resource is freed, another build can use it while the previous build continuous with its business somewhere else. This is the fundamental idea of the so-called **Delivery Pipeline**, core practice of Continuous Delivery.

### Delivery Pipeline in Jenkins ###

Until December 2014, Jenkins relied on few plugins to mimic Delivery Pipelines. All of them were based on chained jobs, in a way that one build (*upstream*) would trigger the next ones (*downstream*) based on its results. The [Parameterized Trigger plugin](https://wiki.jenkins-ci.org/display/JENKINS/Parameterized+Trigger+Plugin) is still a must-have plugin that serves this and many other purposes. To provide a better visualization of the pipeline itself, two plugins became very popular: [Build Pipeline plugin](https://wiki.jenkins-ci.org/display/JENKINS/Build+Pipeline+Plugin) and [Delivery Pipeline plugin](https://wiki.jenkins-ci.org/display/JENKINS/Delivery+Pipeline+Plugin).

With those tools and proper infrastructure, one could easily add some degree of concurrency by simply splitting a single job into several minor jobs (*stages*) triggered one after the other. The implementation of a Delivery Pipeline and its benefits are very well described by Jez Humble in [this chapter of his book](http://www.informit.com/articles/article.aspx?p=1621865) (here called *Deployment Pipeline*).

Still, build flows became more demanding, requiring more control and dynamicity, yet with the same reliability of sequential builds. That's when [Jesse Glick](https://github.com/jglick) introduced the [https://github.com/jenkinsci/workflow-plugin](Workflow plugin) for Jenkins.

### The Workflow plugin ###

The Workflow plugin is a proof that absolute control (or nearly that) can only be achieved with code **:-)**

Having a GUI is very nice indeed. But even simple structures like loops and branches would easily become a mess if implemented in that interface. The visualization becomes impossible as the complexity of the build flow increases. Having the complete build described as code (*Groovy*, btw) allows for better modelling, reusage of functions and handling of states as variables, among other features. Nearly every control mechanism used in a regular programming language can be seamlessly applied to the build environment.
What's more, build flows could be kept under SCM and have much better validation mechanisms.

In addition to those intrisic features, the Workflow plugin has two very helpful components: first is a *DSL language* to represent the most used methods and plugins (including custom ones) in a more natural language; second, a *code snippet generator* to convert existing plugins into inline calls meant to be simply copied into the build.

The documentation for the Workflow is still growing as the community keeps improving the plugin. There is still a lot of work ahead in terms of compatibility: existing plugins must be adapted, although many of them require just minor changes. Since this step should be proactively taken by the main developers of each individual plugin, this may be the point where old and no longer maintaned plugins will become deprecated and leave the game.

If you feel like implementing a pipeline tonight -- and I would recommend that **:-)** -- check out the references I'm leaving below. This is the material I currently rely on whenever necessary. Most of it comes from official means (Jenkins core or Workflow developers). [*Cloudbees*](wwww.cloudbees.com) guys provide webinars on this topic. Talking of it, *Cloudbees* maintains also a [Jenkins Enterprise]() environment with extra plugins, including the [Stage View]() as a really friendly interface for the Delivery Pipeline:



Finally, the references:

Workflow Getting Started

Workflow Basic syntax

Workflow SCM

Example of a Workflow script from Cyrille leClerc.


If *Continuous Integration*, *Continuous Delivery* and *Delivery Pipeline* still do not make any sense for you, check out the references from [this post](/post/continuous-delivery-references/).
