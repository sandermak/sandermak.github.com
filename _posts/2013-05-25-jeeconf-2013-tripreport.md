---
layout: post
title: "JEEConf 2013 trip report"
category : conferences
tags : [conferences, java, modularity]
excerpt: "Like [last year](/blog/conferences/2012/05/jeeconf-tripreport), I had the opportunity to speak at JEEConf Kiev, the leading Java conference in Eastern Europe. More than 700 people from countries like Ukraine, Russia and Belarus attended JEEConf. Most talks are therefore in Russian. However, there was just enough English content to keep me entertained as well."
---

This year, the conference lasted two days rather than one. Somehow, this provided a bit more relaxed atmosphere. Unfortunately I had to miss out on some good content the second day, since I had to leave early to catch my flight. 

![The venue](/pics/jeeconf13_venue.jpg)

_(the conference venue)_

### The content
JEEConf started with a keynote by Reza Rahman on Java EE 7 and 8. To be honest, I think this talk is getting a bit stale. Not that the content is bad, but I've heard (slight variations) of this talk by different Oracle people for more than two years now. It's good to tell a consistent story, but a bit excitement might be good for a change.

I enjoyed the session on [Arquillian](http://arquillian.org) and some of its extensions. Arquillian isn't really new to me, but I hadn't been actively following its development. This was a nice status update, showing many improvements that are very useful in integration test scenario's. For example, being able to have data-driven integration tests using the Persistence extension (based on DBUnit). Another cool extension is the Selenium integration of Arquillian, called Arquillian Drone. You can deploy your app with Arquillian, get the resulting URL injected into your test class and run Selenium tests for different browsers easily.

Another highlight were the talks bij Venkat Subramaniam, also known as [venkat_s](http://twitter.com/venkat_s). His talks are packed with practical knowledge, an enormous amount of energy and humor as well. His performance was even more impressive given the fact that his laptop broke down minutes before the session. So Venkat did all his live coding on a borrowed machine from one of the participants. The first talk was a practical guide to picking a JVM language like Scala, Groovy or Clojure. This lead to a good discussion on whether such a switch is even feasible for 'typical' Java developers. Yakov Fain articulates his point in a [blog post](http://yakovfain.com/2013/05/25/how-to-bring-scala-to-enterprise-it/). Even though I don't fully agree with his conclusions, it is a good discussion.

![Venkat_s in action](/pics/jeeconf13_venkats.jpg)

_(Venkat\_s in action)_

The second talk by Venkat was on concurrency with [Akka actors](http://akka.io). While it's easy to label something as the Next Big Thing™, I believe Akka really is a big deal. It has solid engineering behind it and opens up new doors for highly concurrent applications. With their cluster-implementation maturing as well, we're going to hear a lot more about Akka in the near future.

### Modularity in the Cloud
My talk 'Modularity in the Cloud: a case study' was on the fourth timeslot in the second biggest room. This was my first conference session as a representative of [Luminis Technologies](http://luminis-technologies.com) which I joined March this year. Around 150 people attended the session and judging by the insightful questions and positive feedback, I think the message came across very well. Here are the slides:

<iframe src="http://www.slideshare.net/slideshow/embed_code/21834897" width="425" height="355" frameborder="0" marginwidth="0" marginheight="0" scrolling="no">embed</iframe>
   

The case study focussed on PulseOn, an educational system that Luminis Technologies is building. If you want to find out more about this system, check out the [PulseOn website](http://pulseon.nl/indexEN.php). On the technical side, the open-source [Amdatu](http://www.amdatu.org) project is where you should look. I'll write a dedicated post on the actual content of the talk later. Applying modular architecture is a very relevant message for contemporary Java developers, even though there are no easy answers. So watch this space.

### The people
Besides technical content, the other important part of a conference is meeting people and exchanging ideas. Unlike last year there was a speaker's dinner which provided ample opportunities for exchanging ideas and having a good time.

It was nice to see [Yakov Fain](http://twitter.com/yfain) again, whom I met previous year at JEEConf as well. I should also mention [Mohamed Taman](http://twitter.com/_tamanm), an active Java community member who is doing some [great work](http://tamanmohamed.blogspot.nl/search/label/JCP) for the JCP. I also talked to [Vladimir Miguro](http://twitter.com/trnl) (aka vova), who is working on the new MongoDB 3 Java driver. It's interesting to hear how it will to totally revamped to provide a more fluent and idiomatic Java interface. Fear not, there will be a v2 compatibility layer for those who have non-modular applications that cannot easily cope with changes in underlying API's :-)

Last but not least, props to [Mikalai](http://twitter/xpinjection) and Aleksey, who once again organized a great Java conference!
