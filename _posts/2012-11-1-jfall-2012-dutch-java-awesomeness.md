---
layout: post
title: "JFall 2012: Dutch Java awesomeness" 
category : conferences 
tags : [conferences, speaking, java, security]
excerpt: "The J-Fall conference has been a stable factor in the Dutch Java community for many years now. The 2012 edition was again jam-packed with excellent content. But above all, it's a place where more than thousand like-minded developers can meet (again) and exchange fresh new ideas!"

---

Right from the beginning it was clear that this year's keynote would be much more dynamic than last year. Stephen Chin drove his motorcycle (yes, you read that right) into the keynote hall. What followed was a whirlwind of short presentations, outlining the major themes for Java in the coming years: Java 8/9, JavaFX 2.2, Java Embedded for ARM and Project Easel for deep HTML5 integration in NetBeans. Nothing that wasn't announced at JavaOne, but still a good overview.

### Morning sessions
The [first break-out session](http://www.nljug.org/pages/events/content/jfall_2012/sessions/00096/) I attended was on Unfiltered. Wilfred Springer aptly called it an 'unframework' because it is so small, and focused solely on HTTP request handling. After some live coding (with audience participation where necessary) it became clear that this Scala framework is quite coherent and capable.  

Next up was a ['Calling time on performance measurement'](http://www.nljug.org/pages/events/content/jfall_2012/sessions/00081/) by William Louth. I was half expecting a product pitch on JXInsight, but fortunately he focused not just on the tool but also on an interesting philosophy about adaptive systems in general. Most applications are fairly 'dumb' in a sense that they keep repeating sub-optimal behavior. Things like hammering a database that is down, or even fairly advanced cases like sub-optimal use of a HashMap by program code. Louth proposed [Signals](http://www.jinspired.com/solutions/open-api) as a way to build a runtime profile of such behaviors. In turn, the program can use these profiles to guide subsequent executions on a better path. Interesting, but I'm not sure how usable the whole Signals approach currently is. Even though the demo's were kind of jittery and (too) fast-paced, this session gave me some food for thought.

### NightHacking
I 'missed' the second keynote by Microsoft because I was being interviewed by Stephen Chin about my upcoming [cross-build injection attacks](/blog/security/2012/03/crossbuild-injection-how-safe-is-your-build) session. Stephen is traveling Europe with a motorbike on his way to Devoxx 2012, called the [NightHacking](http://steveonjava.com/nighthacking) tour. Meanwhile he live-streams interviews with various Java experts. It was cool to be part of the NightHacking tour, and at least I have the sticker to prove it :) 

![NightHacking sticker](/pics/nighthacker.png)

You can watch the recorded interview here:


<iframe width="600" height="338" src="http://www.youtube.com/embed/44Ke-XxsgOc?feature=player_detailpage" frameborder="0" allowfullscreen="">embed</iframe>


<br>
<br>

### CQRS and event-sourcing
One last talk I want to highlight is ['High performance and scalable architecture'](http://www.nljug.org/pages/events/content/jfall_2012/sessions/00091/) by Allard Buijze. He showed us a real-life example of a CQRS and event-sourcing based architecture for BridgeBig.com, an online bridge gaming site. The talk was very down-to-earth with some good examples on how to split up your system in functional areas that exchange (stateless) Commands and Events. Since Allard is the author of the Axon CQRS framework, it was used heavily in this system. Even in combination with the [Disruptor](http://lmax-exchange.github.com/disruptor/) pattern for high throughput within a single sub-system. I will have to investigate Axon, but think I think the same can be achieved with for example [Akka](http://akka.io/) actors and an accompanying [event-sourcing library](https://github.com/eligosource/eventsourced). That would be my personal preferred stack, on top of Scala.

### Cross-build injection revisited
For the fourth year in a row I was able to present at J-Fall. This year I did a (Dutch) re-run of my [JavaOne talk](https://oracleus.activeevents.com/connect/sessionDetail.ww?SESSION_ID=3892) 'Cross-build injection attacks: how safe is your Java build?'. The session was scheduled on the last slot of the conference. Some people already left the building, but still there was a nice crowd. I always ask how many people have heard about cross-build injection and build security. Typically about 2-3% of the audience raises their hand, which is heartening to me because I can teach something new. Good questions where asked on actual steps people should take to prevent cross-build injection. I believe developers are starting to see that what's most convenient for them in the build process is not necessarily what's in the best interest of the safety of the end-product. 

![me at the start of the session](/pics/jfall12.jpg)
(me at the start of the session, photo courtesy of [@byteheads](https://twitter.com/byteheads))

It was especially nice that I could point out that Maven Central is now [https accessible](http://www.sonatype.com/people/2012/10/now-available-ssl-connectivity-to-central) as well. This is a big step forward in comparison to the previous incarnation of this talk at JavaOne. I'll blog some more details about this later.

### See you next year
Overall, I was impressed with the quality of content delivered by the Dutch Java community and the various international speakers at J-Fall. But it was also a great meeting point for old friends, (former) colleagues and many other interesting people. Until next year!

