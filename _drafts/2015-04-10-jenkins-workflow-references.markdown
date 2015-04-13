---
layout: post
title: Jenkins Workflow references
---

[Jenkins](https://jenkins-ci.org/) was first released in February 2005 (then Hudson) as a server-based Continuous Integration platform and soon became the leading application in this domain. Its rapid growth and success is partly because Jenkins is open-source from conception. The extension mechanism allowed hundreds of developers to collaborate through small and independent plugins for multiple applications. To date, there are nearly 1010 publicly available contributions.

Jenkins was designed so that every build configuration could be described in a separate self-contained *job*. Every job has, among other features, sequential build steps and post-build steps which are executed once a build is requested. In the meantime, further requests are queued, waiting for the resources (aka *executors*) to become available once again. Here it's noted that the complete package comes with a nice and clean GUI, which is part of the beauty of Jenkins and probably contributed to allure those who preferred this experience over plain-text Maven, Ant etc.

Even so, this design regards the job as a single resource, assigned at the beginning of the build execution and held until the very end. In practice, however, every build and post-build step may require a different set of resources. Envisioning the job in a finer granularity automatically prompts the idea that, in fact, multiple build requests could be handled concurrently with the same set of atomic resources. This is the fundamental idea of the so-called **Delivery Pipeline**, core practice of Continuous Delivery.

### Delivery Pipeline in Jenkins ###

Until December 2014, Jenkins relied on few plugins to mimic a Delivery Pipeline. All of them were based on chained jobs, in a way that one build (*upstream*) would trigger the next ones (*downstream*) based on its results. The [Parameterized Trigger plugin](https://wiki.jenkins-ci.org/display/JENKINS/Parameterized+Trigger+Plugin) is still a must-have plugin that serves this and many other purposes. To provide a better visualization of the pipeline itself, two plugins became very popular: [Build Pipeline plugin](https://wiki.jenkins-ci.org/display/JENKINS/Build+Pipeline+Plugin) and [Delivery Pipeline plugin](https://wiki.jenkins-ci.org/display/JENKINS/Delivery+Pipeline+Plugin).

With those tools and proper infrastructure, one could easily add some degree of concurrency by simply splitting a single job into several minor jobs (*stages*) triggered one after the other. The implementation of a Delivery Pipeline and its benefits are very well described by Jez Humble in [this chapter of his book](http://www.informit.com/articles/article.aspx?p=1621865) (here called *Deployment Pipeline*).

Still, build flows became more demanding, requiring more control and dynamicity, yet with the same reliability of sequential builds. That's when [Jesse Glick](https://github.com/jglick) introduced the [https://github.com/jenkinsci/workflow-plugin](Workflow plugin) for Jenkins.

### The Workflow plugin ###

The Workflow plugin is nothing more than the conclusion that nothing can provide more control than code itself. It's the exact science. As the GUI challenges became more unreliable to handle such complex build structures.



completely blocked untilOnce a build is requested, it executes every build step until these are finished (or interrupted with a failure) followed by the

In Jenkins, every build configuration is described in a separate self-contained job

Ten years later, the most important tool of Continuous Delivery was still an open issue: how to implement a proper Delivery Pipeline within Jenkins environment?
The 
there was still an issue that no plugin could solve:

All in all, there is plenty of information about Jenkins and its benefits out there. More recently, 




**One year into Continuous Delivery!** As my daily work happens within the walls of a **huge** company in the automotive industry, several were the challenges I had to deal with so far. The environment is very much heterogeneous. Tens of teams, often with hundreds of people each, constantly feeding their build servers with code changes coming from all around the world.

> *"Until your pretty code is in production, making money, or doing whatever it does, you've just wasted your time."* (Chris Read - [@cread](https://twitter.com/cread))

Every aspect of a build chain is fundamentally relevant, from the initial commit until the product is packed, ready to ride on a real car. Build trigger policies, quality of the feedback to developers, parallelism and load distribution tend to be quite complex issues in such corporations. Even organizational questions such as plugin distribution mechanism across multiple departments have an impact in the quality and productivity.

After all, this is an industry in which companies should be able to reliably rebuild their products after 20+ years! Complex!

That said, it's quite difficult to summarize what one could learn in such environment in terms of Continuous Delivery. Nevertheless, I have a selection of solid references where I started to build my foundations from the beginning. Just to contextualize, my daily tools are [IBM Rational Team Concert](http://www.ibm.com/software/products/en/rtc) and [Jenkins](http://jenkins-ci.org), which is nowadays a synonym of Continuous Integration.

So, here we go...

* [**Continuous Integration**](http://martinfowler.com/articles/continuousIntegration.html) by *Martin Fowler*

* [**Continuous Delivery: Reliable Software Releases through Build, Test, and Deployment Automation**](http://martinfowler.com/books/continuousDelivery.html) by *Jez Humble* and *David Farley*. [Chapter 5](http://www.informit.com/articles/article.aspx?p=1621865) publicly available at *InformIT*

* [**Continuous Delivery**](http://www.infoq.com/interviews/farley-continuous-delivery) by *Dave Farley*

* [**Orchestrating Your Delivery Pipelines with Jenkins**](http://www.infoq.com/articles/orch-pipelines-jenkins) by *Andrew Phillips* and *Kohsuke Kawaguchi*

* [**Continuous Integration anti-patterns**](http://www.ibm.com/developerworks/java/library/j-ap11297/) by *Paul Duvall*

* **Jez Humble on Continuous Delivery**

-> <iframe width="560" height="315" src="https://www.youtube.com/watch?v=skLJuksCRTw" frameborder="0" allowfullscreen></iframe> <-

