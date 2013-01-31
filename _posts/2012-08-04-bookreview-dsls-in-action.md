---
layout: post
title: "Book review: DSLs in Action" 
category : bookreview 
tags : [java, scala, dsl, book]
excerpt: "Domain-Specific Languages are not the most used tools in the toolbox of developers. According to the author of 'DSLs in Action' this is a missed opportunity: these little languages can deliver lots of benefits. In this book he argues this position with fervor. But be warned: this is not a book for beginners."
bookurl: http://www.amazon.com/gp/product/1935182455/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=1935182455&linkCode=as2&tag=branandboun-20
bookimg: dsls_in_action.png
booktitle: DSLs in Action
bookauthor: Debasish Ghosh
bookpub: Manning (2010)
---


[DSLs in Action]({{ page.bookurl }}) consists of three main parts and a well-filled appendix. The first part introduces the term DSL and shows the benefits of DSL-driven development. It's also made clear that the purpose of DSLs is not to allow the business to program, but to help developers write code that can be a means of communication with the business. Ultimately, a DSL is a way to achieve 'beautiful abstractions'. The introduction is followed by concrete examples of DSL implementations in Java and Groovy. But obviously, without a domain there is no DSL. Therefore, throughout the whole book the stock trading domain is used in all examples. The author takes the time to thoroughly describe this domain, so the examples are non-trivial and really fit the narrative.

### Internal DSLs
In the second part the so-called 'internal DSLs' are described. These are languages ​​embedded in an existing programming language. Of course the expressiveness of the existing programming language determines how successful such an embedding can be. Examples are provided using both dynamic languages ​​and statically typed languages. The dynamic examples use concepts such as meta-programming, macros, and monkey-patching using Groovy, Clojure and Ruby. The statically typed examples are all written in Scala, and clearly demonstrate the value of the type-system for enforcing domain-specific rules. Fortunately you don't have to be an expert in those languages​​, because the book contains appendices with an introduction and a cheat sheet for each of the mentioned languages.

### External DSLs
The second part then continues with 'external DSLs', which are separate languages with their own language definition and associated parser. This is often what people think of first when talking about DSLs. But it is not without reason that this alternative is discussed later. External DSLs require much more effort because you are not reusing the infrastructure of an existing programming language. Also, the added flexibility offered by external DSLs is not always necessary. 

The first example of an external DSL is developed with ANTLR, a parser generator framework. Meanwhile, much is explained about parsing and grammars. The author doesn't shy away from the underlying theoretical foundations where necessary. Next, an example is given for the Eclipse XText framework. XText language definitions deliver both a language parser and an Eclipse editor plugin for your DSL, with syntax highlighting, code completion and everything you're used to with Eclipse for Java.

Finally parser combinator libraries are discussed. Parser combinators allow you to express a grammar as internal DSL in a general purpose language. Scala's parser combinator library is used to show how you can create a parser without code generation, unlike the previous two external DSL examples.

### Closing remarks 

The last part looks at expected developments in the field of domain-specific languages. It feels a bit strained and doesn't offer many new insights. But that's okay, since the previous parts are more than excellent. Seldom have I read a technical book which is both wide, deep and practical. Even if you do not believe in DSLs as a concept, then this book is packed with useful information. For example, it is also an excellent introduction to 'polyglot programming' (combining different programming languages).

A similar book has been published by Martin Fowler: [Domain-Specific Languages](http://www.amazon.com/gp/product/0321712943/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0321712943&linkCode=as2&tag=branandboun-20). However, Fowler explicitly focuses on an Object-Oriented approach to DSLs, whereas DSLs in Action takes both the OO and functional approach. In my opinion you can't ignore functional languages when talking about (internal) DSLs.

All in all, [DSLs in Action]({{ page.bookurl }}) is a top-notch book that I heartily recommend.

_(a Dutch version of this article was print in the [Dutch Java Magazine](http://javamagazine.nl/))_

