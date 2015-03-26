---
layout: post
title: Continuous Delivery references
---

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

<iframe width="560" height="315" src="https://www.youtube.com/watch?v=skLJuksCRTw" frameborder="0" allowfullscreen></iframe>


