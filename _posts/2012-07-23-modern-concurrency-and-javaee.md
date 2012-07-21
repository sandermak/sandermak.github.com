---
layout: post
title: "Modern concurrency and Java EE" 
category : java 
tags : [java, concurrency, javaee]
excerpt: "Managing your own threads within a Java EE container is not recommended and even illegal in some circumstances. But where does that leave us when we want to use the shiny new Java concurrency frameworks in Java EE applications? The addition of new concurrency facilities to the Java EE 7 spec may open some doors."

---

The past year I've been diving into concurrency on the JVM. Java SE 7 brought us [Fork/Join](http://docs.oracle.com/javase/tutorial/essential/concurrency/forkjoin.html) concurrency, and other other approaches like [actor-based](http://akka.io) concurrency with for example Akka gain popularity as well. When talking about Fork/Join (talk on [Parleys](http://www.parleys.com/#st=5&id=3217), [slides](http://www.slideshare.net/SanderMak/fork-join-bejug-2012)) and Akka (talk on [Parleys](http://www.parleys.com/#id=3218&st=5), [slides](http://www.slideshare.net/SanderMak/akka-bejug/)) one question I always get is: 'can use this within a Java EE application server?' 

Since both Fork/Join and Akka start and stop their own thread(pool)s, the answer is: 'it usually works, but it is not recommended within Java EE containers'. Unsatisfactory answer? You bet. But fortunately things may change with the next release of Java EE.

## JSR-236: Concurrency Utilities for the Java EE platform
Most application servers already offer the possibility to get a managed (sort-of, kind-of) threadpool through the [WorkManager](http://docs.oracle.com/javaee/1.4/api/javax/resource/spi/work/WorkManager.html) API. It was never standardized as part of Java EE though. And even if it was, it would not be enough for the Fork/Join framework. The only pluggability you get when instantiating a [ForkJoinPool](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/ForkJoinPool.html) is a custom thread factory. For the record, it seems that Akka [can be configured](http://blog.vasilrem.com/container-managed-actor-dispatchers) with WorkManagers.

However, standardization of container-managed threading may be upon us. Work on JSR-236 commenced in 2003. Yes, that's almost a decade ago. The first draft of the Concurrency EE spec appeared in 2006, but unfortunately it languished ever since. In April of this year the JSR was resuscitated and an [updated version](http://java.net/downloads/concurrency-ee-spec/EE%20Concurrency%20Utilities.pdf) was published last month. Even though the [delta](http://java.net/downloads/concurrency-ee-spec/EE%20Concurrency%20Utilities%20-%20delta.pdf) with the 2006 draft is minimal (e.g. 'update package name from javax.util.concurrent to javax.enterprise.concurrent'), it is good to see things are back on the agenda.

## javax.enterprise.concurrent
So what's in this elusive new javax.enterprise.concurrent package? I salvaged the following diagram from a 2006 JavaOne [presentation](http://gee.cs.oswego.edu/dl/concurrencyee-interest/JavaOne-EEConcurrency.pdf), with the new concurrency facilities marked yellow:

![Java EE concurrency](/pics/javaeeconcurrency.png)

The two most important interfaces are [ManagedExecutorService](http://gee.cs.oswego.edu/dl/concurrencyee-interest/docs/javax/util/concurrent/ManagedExecutorService.html) and [ManagedThreadFactory](http://gee.cs.oswego.edu/dl/concurrencyee-interest/docs/javax/util/concurrent/ManagedThreadFactory.html), both extending their non-managed java.util.concurrent counterparts from Java SE. What does this Managed prefix buy us? It means that tasks submitted and threads created through these interfaces will be fully aware of the Java EE contextual services. Things like transaction management, security context and so on are all available when the task is executed or the thread is used. 

Another interesting aspect of the propesed ManagedExecutorService is that it must support distribution. One of the following properties can be configured:

- _Local:_ the task is run in the same process/server that submitted the task.
- _Distributable:_ the task may be run in any process/server including the one that submitted the task.
- _Distributable with Affinity:_ the task may be run in any process/server including the one that
submitted the task. All tasks will then run on the selected process/server.

Obviously, the tasks need to be Serializable for this to work.

## Now what?
When reading the JSR-236 spec it is clear that it was crafted in the ancient J2EE times. Just look at the code examples in the draft spec (EJBHome interfaces, really?).  CDI is never mentioned, and neither is Fork/Join. Some of the work in there is superseded by the EJB 3.1 @Asynchronous annotation and EJB Timer facilities. Still, it's clear that this spec will lay the groundwork for integrating modern Java concurrency frameworks with Java EE. But exactly how this will work out is too early to tell.

Another obvious candidate for using the javax.enterprise.concurrent functionality is the [Batch Processing](https://blogs.oracle.com/theaquarium/entry/new_java_ee) JSR. There's lot's of work to do it seems. Let's hope that expert groups find enough time to join forces and move the Java EE platform forward.

