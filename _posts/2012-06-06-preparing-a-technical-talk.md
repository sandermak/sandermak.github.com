---
layout: post
title: "Preparing a technical talk"
category : conferences
tags : [conferences, speaking]
excerpt: "We software engineers love giving talks. Fortunately, the abundance of technical conferences shows that we like listening too. A lot of talks on these conferences are given by software engineers who love sharing knowledge, rather than professional speakers. This especially holds true for conferences organized by user groups. Since I'm one of those software engineers who likes to give talks, I'd like to share my thoughts on what you can do to prepare a technical talk."
---

In the past few years I've gained some experience with presenting at conferences. In fact, I started writing this post en-route to JEEConf where I'll give two talks (trip report [here](http://branchandbound.net/blog/conferences/2012/05/jeeconf-tripreport)). While I enjoy doing this, I'm by no means a professional speaker. It's really a matter of learning-by-doing. To me, giving a talk in front of a like-minded audience is exhilarating, and striking up interesting conversations afterwards can be really rewarding. But don't be fooled: even though your performance may last for only an hour, you'll be investing a lot of time to make it go just right.
 
## How to start
Let's say you have a wonderful topic in mind for a talk: 'Peer-to-peer IDEs using Websockets and Git' (fictional of course, but it sounds cool. Someone should do this). You've worked on the topic for some time, but it's time to share it with the world. Now, what will be your first step? Making slides? Coming up with a witty title? Start hacking on your demos? No. You start by asking yourself the two very important questions:

1. Who is my audience?
2. What is my message?

Is your audience into specific programming languages and frameworks? Does it consist of experienced coders, or a mix of technical and 'business'  people? What are their preconceptions? All of this information helps you determine the central message of your talk before you've made a single slide. For example, our topic is a bit out there and wouldn't really fit a conference with your typical 'enterprise developers' audience. You might tone down the topic a bit in this case, focusing on a single aspect, or simply choose a different audience... 

After you've profiled the audience, it is time to formulate the message of your talk. No, your title is not your message: titles can be provocative teasers, whereas your message succinctly describes what you want to convey with your talk. Think of it as the core sentence of your abstract. Of course this core message must be tailored to your audience. The message defines the focus of all remaining efforts. Our fictional topic, for example, can be brought with several different messages:

- 'Explain how Websockets enable cool applications like a collaborative P2P IDE that leverages Git'
- 'P2P collaborative IDEs are the way of the future, and here's how you could implement them using Websockets and Git'
- 'How Git can be used as building block for new applications'

Each of these messages leads to a vastly different narrative. Reach back to the core message every time you need to decide whether to include or omit a sub-topic. Does it strengthen your message? Above all, remember: the message should answer a question that is relevant to your audience. If not, both you and the audience will be wasting time.

## Focus
A big pitfall for me is that I tend to focus too hard on the subject. This may sound strange, so let me try to explain. Doing research is necessary when preparing a technical talk. However, it is very easy to overdo this. It's not necessary to read every single blogpost ever written on the topic at hand, to delve into the source of a library, or follow a framework's mailinglist religiously. You'll tell yourself that you are getting a deeper understanding by researching so much, but remember you're not doing this (just) for you! 

In the end what counts is giving a clear and concise talk to your audience. Having more information in your head does not automatically lead to a better presentation. Let the message guide the direction and amount of research that you perform on each sub-topic. There will always be someone in the audience who knows more about the topic than you. It'll be fine, trust me.

## Slides
There's lots of [excellent advice](http://www.presentationzen.com/) already on using and designing slides which I won't repeat here. A lot of it boils down to 'do less'. I have a lot of respect for speakers who deliver talks without a slide deck. But it's not for me, at least not now. Having [slides](http://www.slideshare.net/sandermak) to guide me through a talk is essential for me. Additionally, having visuals like diagrams and graphs always helps in technical talks. Avoid the stereotypical slides with endless bulleted lists and you're ahead of the pack already.

## Code examples
Remember the P2P IDE using Websockets and Git? You can talk about it all you want, but showing code is infinitely more effective. That is, if done right. One of the worst presentations I ever saw was simply playing a screencast for the demo parts. Aside from the unreadable pixelated text (if you ever do this, use a proper codec) it was just plain boring to watch a pre-cooked movie. Keeping the narration in sync with the screencast was awkward. Just don't do this, however safe it may feel.

The other extreme is live coding. Doing live coding in front of an audience is risky, but the rewards can be commensurate. What's better than watching a skilled craftsman doing some awesome coding? Not every topic lends itself for this approach, though. You need to be able to build something from scratch without too much distracting boilerplate. Preferably building out a single example throughout the talk. 

I tend to choose the happy medium: walking through some actual code in the IDE which is prepared beforehand, occasionally adding a bit. You run the risk of overwhelming the audience with this approach, so there's a few tricks I picked up. First, whenever possible introduce the demo with a diagram showing the big picture before switching to the IDE. Second, minimize the amount of scrolling back and forth and switching between files. This takes some careful planning, and IDE features such as code folding can really help in this regard. And, on a more practical note, make sure the font is readable throughout the whole room!

![Code folding in Eclipse](/pics/codefolding.png)

When code is not really essential to the talk, or you just need to show some standalone snippets, put it into a slide. Yes, it's a bit more static, but not having to switch between slides and IDE all the time is worth something as well. Just promise me you'll leave the laserpointer at home. Number the lines and refer to these linenumbers if you need to. Anything is better than a jittery red dot on the screen.

## Showtime
So you've got your slides and demos ready. Now what? Some people like to do a 'dry-run' of the presentation just by themselves. You'll familiarize yourself with the slides and the flow of the presentation, and find out whether you finish early or late. While this can be an excellent preparation, for me this doesn't really work. There's something slightly odd about talking to a void. If I decide to do a dry-run, I try to organize a few colleagues. As added bonus you'll get early feedback to polish your talk. When this is not feasible, I try to verbalize my talk for at least the first three slides. This makes me think less when getting up in front of the audience. Having a flying start is a real confidence booster for the rest of the talk.

In the end giving good technical talks is a matter of doing it often. Combining deep technical knowledge with the performance art of public speaking is a valuable skill, and not just in the context of giving talks at conferences. Whenever you communicate with your customers about software requirements and technical issues, you'll find yourself using the same skillset. So I urge you to just try and prepare a talk on a framework or programming language you love. Present it to your co-workers, or at a user group meeting and keep improving!


