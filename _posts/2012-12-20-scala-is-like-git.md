---
layout: post
title: "Scala is like Git" 
category : scala 
tags : [scala, git]
excerpt: "Scala is often portrayed as being too complex. Many times these discussions about complexity remind me of similar arguments around the complexity of Git. I know, the comparison between a source control system and a programming language falls flat quickly when pushed too hard. But bear with me, because I do believe it can put the Scala complexity discussion in a new perspective."

---

Both Git and Scala are power tools, meant to disrupt the status quo rather than incrementally improving it. Git isn't just a better CVS/Subversion: its distributed nature makes it fundamentally different. The same goes for Scala: it's not just a better Java, but a daring entrant into the hybrid FP/OO arena. Interestingly, the complexity arguments are often framed by comparing Git or Scala to fundamentally more limited technologies. Yes, obviously whatever you were doing in Subversion or Java may be much simpler. What should be at the forefront of these discussions though, is what more you can achieve using Git or Scala. It's not just about the (complexity) costs, but also about the benefits. Do you buy into the vision behind these power tools?

### Power tools require investment
It has taken me a solid year of actually working with Git in a team-setting to appreciate all the advantages it has to offer. Before this period, I played around with it, mainly using GitHub, but many questions remained (why do I need a staging area? Why are there three ways to do the same thing?). Trusting people who went before me and embracing Git brought me a whole new level of productivity in the end. Scala's learning curve is similar. Even though I have a background in functional programming from my university days, there still was a lot to take in. But like with Git, for me the advantages far outweigh this awkward period of embracing a tool you don't fully 'get' yet.

Such an investment definitely poses a barrier to entry. However, Git has overcome this barrier by offering something very desirable: effortless branching and merging (or [rebasing](http://git-scm.com/book/en/Git-Branching-Rebasing), love that). Likewise, Scala's gateway drug is [pervasive parallelization](http://lampwww.epfl.ch/~phaller/upmarc/) and an [integrated stack](http://typesafe.com/stack) of Scala, Akka and Play. 

### Clean core
What I like most about Git and Scala is that both embrace the approach of having a clean core of basic concepts. Git has its [Directed Acyclic Graph](http://eagain.net/articles/git-for-computer-scientists/) of commits, trees and blobs. Every operation is essentially a transformation of the graph (and/or refs into the graph, to be precise) . Complex? Maybe, but once you grasp it, you have so much power. A similar case can be made for Scala. Even though the amount of concepts is larger - it is a programming language after all, not a VCS - to me all language features show regularity both in the small and in the large. Everything can be nested and composed just like you would expect. 

That Scala has a relatively small core is illustrated by the fact that the Scala Language Specification is quarter the size of the Java Language Specification. Scala's grammar is smaller as well. Let me be quick to add that specification size is by no means a token of superiority in and of itself (cf. [Brainfuck](http://progopedia.com/language/brainfuck/)). But it is congruent with this great quote by Martin Odersky:

> I have always tried to make Scala a very powerful but at the same beautifully simple language, by trying to find unifications of formerly disparate concepts.

This philosophy really resonates with me. Admittedly Scala doesn't fully unify everything that it could. It's also a very practical language with practical tradeoffs, for example concerning Java interop. Such is life.

### Complexity
Even though Git and Scala have clean and composable primitives, the _surfaced complexity_ is relatively high. Git's CLI has an [abundance of commands](http://stackoverflow.com/questions/7866353/git-list-all-available-commands) and is [notorious](http://steveko.wordpress.com/2012/02/24/10-things-i-hate-about-git/) for its inconsistent flag usage. Scala's syntax has some [surprises](http://www.slideshare.net/normation/scala-dreaded) for newcomers as well. And yes, Scala may have a [feature](http://programmers.stackexchange.com/questions/179699/whats-the-problem-with-scalas-xml-literals) or [two](http://stackoverflow.com/questions/13011204/scalas-postfix-ops) too many. If you know the underlying core model that ties everything together, such issues are annoying but you'll get over them. It's a form of [accidental complexity](http://c2.com/cgi/wiki?AccidentalComplexity). I do applaud the efforts of the Scala team to [modularize language features](http://docs.scala-lang.org/sips/pending/modularizing-language-features.html), since it allows for gradual rectification of these issues!

In the end, being able to work with the elegant core models of these tools is worth the price of admission to me and many others. Yes, you will have some rope to hang yourself with both Git and Scala. Deal with it.

### GUIs
But it doesn't stop there. One of the most important things for wide adoption, especially when taking into account the 'enterprise crowd', is having good GUI support. Git GUIs and IDE support for Scala have long been an afterthought. And I'm not even sure whether GUIs for Git actually reduce complexity. I tried [a](http://code.google.com/p/gitextensions/) [few](http://code.google.com/p/tortoisegit/), but didn't like it. For Scala IDE plugins though, it's a no-brainer. Being able to navigate types and implicits in large codebases is invaluable. Fortunately, the Scala Eclipse plugin is now backed by [Typesafe](http://typesafe.com/) and is moving forward quickly. IntelliJ's Scala plugin is still going strong as well. I'd say Scala is ahead of Git in this regard, though not ahead of other programming languages to be fair.

### Real-world usage
One thing that becomes apparent when adopting Git is that it doesn't prescribe a particular workflow. Hence, many different workflows have been championed. Some even including tool support, like git-flow and Legit. Does this indicate a shortcoming with Git? Is it too complex on its own? I don't believe so. It's just that people are looking for common ground and don't want to reinvent the wheel. But it is telling that none of these workflows and supporting tools have really won out. Diversity is key it seems.

For Scala, things are a bit different. Adoption is increasing, but the community is still looking for a common 'tone-of-voice'. For a programming language this is much more crucial, especially an expressive one like Scala. Java, by contrast, has had almost two decades to mature and develop widely accepted idioms and accompanying styleguides. I believe Scala needs to go through a similar process. The signs that this process has started [are there](http://docs.scala-lang.org/style/). What we need is a definitive Effective Scala book that can lead the way, like [Effective Java](http://www.amazon.com/gp/product/0321356683/ref=as_li_qf_sp_asin_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0321356683&linkCode=as2&tag=branandboun-20) did for Java. 

### The enemy of good is good enough
Why should people switch from SVN to Git, or from Java to Scala? After all, Subversion got merge-tracking and Java 8 will get lambdas. The point is that these additions do not change the fundamental core model of the 'older generation' tools. If anything, it adds unanticipated complexity without truly reaching the next level. It will be good enough for a lot of people though, and I recognize that changing VCS or programming language is not something to be taken lightly. I do urge you to explore new possibilities and see if good enough still cuts it for you.

Then there are the 'current generation' competing technologies. Git had to battle Mercurial and Bazaar. Scala faces potential competition from Kotlin and Ceylon. All these alternatives advertise simplicity or 'intuitiveness' as big selling point. And they may very well be more intuitive initially. I'm very curious whether they have to compromise on their core model and internal design philosophy to surface this intuitiveness. Fusing object-orientation and functional programming is a hard job. I'll definitely revisit this once these new languages are properly released.

In the end, Git has succeeded because it offers tangible benefits, despite its surfaced complexity. So can Scala.

