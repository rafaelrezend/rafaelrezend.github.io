---
layout: post
title: Play! goes Workflow
---

Few weeks ago I managed some time to bring the [*Play! plugin for Jenkins*](/play-plugin-jenkins.html) into the Workflow plugin.
Learning how this migration is done was more relevant for me than the result itself **:)**
**Why?**

The *Play! plugin for Jenkins* was specially useful because of the intuitive GUI aspect of it. It was designed so that *Play1.x*, *Play2.x* and *Activator* could be used in the same fashion, no matter the specifics of each one.
*Play2.x* and *Activator* have different executables, but both use *sbt* underneath as build tool. *Play1.x*, on the other hand, does not use *sbt* at all and every *goal* must be executed in a separate command line. Their goals also differ. The *Play! plugin* was intended to minimize the differences with a single interface.

The Workflow plugin brings the whole story *closer* to command line again. Now, running pure *Play/Activator* commands directly from Groovy is as easy as invoking the Play! plugin itself in the same interface. That explains why the migration may not be that much relevant **;-)** Still it's nice to learn.

Hints on how to migrate your freestyle job to Workflow [here](https://github.com/jenkinsci/workflow-plugin/blob/master/scm-step/README.md).
The already [compatible plugins](https://github.com/jenkinsci/workflow-plugin/blob/master/COMPATIBILITY.md) are also a good reference.

Hints on how to use the *Play! plugin* on Workflow:

 * [*Play! plugin for Jenkins*](/play-plugin-jenkins.html)

Enjoy!