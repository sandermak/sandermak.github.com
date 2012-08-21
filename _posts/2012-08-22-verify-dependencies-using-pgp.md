---
layout: post
title: "Verify dependencies using PGP"
category : security
tags : [maven, java, security, pgp]
excerpt: "Many builds use automatic dependency retrieval from repositories like Maven Central. In an earlier post, I've outlined the dangers of so-called [cross-build injection attacks](/blog/security/2012/03/crossbuild-injection-how-safe-is-your-build) when using automatic dependency management. In this post, I'll show how you can counter such attacks by verifying PGP signatures of artifacts downloaded from Maven Central."
---

There are [various](http://kohsuke.org/pgp-maven-plugin/) [articles](http://www.sonatype.com/people/2010/01/how-to-generate-pgp-signatures-with-maven/) that show how to sign your artifact before uploading it to Maven Central. However, we're more likely to be library users than library authors. Unfortunately, information on how to verify PGP signatures of artifacts is surprisingly scarce. Even StackOverflow seems to [dodge the bullet](http://stackoverflow.com/questions/3307146/verification-of-dependency-authenticy-in-automated-build-systems). So let's fix that.

### Pretty Good Privacy

Pretty Good Privacy (PGP) can be used to encrypt or sign data. It uses asymmetric public/private keypairs. Since libraries on Maven Central are open source, encryption is pointless. Signing, on the other hand, can be used to prove the authenticity of a library. We can use the library authors' public key to verify that the artifact is signed with the corresponding private key. This may sound easy, but in practice this is a multi-step process. If you want to follow along with the signature verification steps in this post, you'll have to install a PGP implementation. I'm using GnuPG ('brew install gpg' on OSX).

### Getting the signature
Before we can verify an artifact, we must first obtain its signature. So let's say we want to use the plexus-cipher library. First, add the dependency to the build (groupId: org.sonatype.plexus, artifactId: plexus-cipher and version: 1.7). Any buildtool that uses Maven Central to retrieve dependencies will do. Of course this can be Maven itself, but also SBT, Gradle or even Ant+Ivy (I'll assume Maven for the remainder of this post). Adding this dependency will prompt Maven to download:


    http://repo1.maven.org/maven2/org/sonatype/plexus/plexus-cipher/1.7/plexus-cipher-1.7.jar
    http://repo1.maven.org/maven2/org/sonatype/plexus/plexus-cipher/1.7/plexus-cipher-1.7.pom

In order to get the accompanying signatures, append .asc to these urls and download them:


    $ wget http://repo1.maven.org/maven2/org/sonatype/plexus/plexus-cipher/1.7/plexus-cipher-1.7.jar.asc
    $ wget http://repo1.maven.org/maven2/org/sonatype/plexus/plexus-cipher/1.7/plexus-cipher-1.7.pom.asc

Even though these signatures live alongside the artifacts themselves, Maven does not download them automatically. Also, beware that not all artifacts in Maven Central have .asc signatures. Up to three years ago, Maven Central did not require library authors to provide PGP signatures.

### Verifying the signature
We have the artifact and the signature, but we still need to obtain the public key of the library author before we can continue. The gpg verify command dutifully tells us just that:

    $ gpg --verify plexus-cipher-1.7.jar.asc ~/.m2/.../plexus-chipher-1.7.jar

    gpg: Signature made Tue Jul 26 20:06:33 2011 CEST using DSA key ID 8DD1BDFD
    gpg: Can't check signature: public key not found 

Now we could of course ask the author for his public key by email, or try to download it from the project's website. That's not really a scalable solution though... Fortunately, Maven Central also requires its uploaders to publish their public key to a PGP keyserver. Specifically, [MIT's keyserver](http://pgp.mit.edu/). The following diagram shows how it works:
![Publishing signed library to Maven Central](/pics/maven_pgp.png)

Armed with this knowledge, we can import the key with ID 8DD1BDFD (as we learned during our failed verification attempt) into our keyring:

    $ gpg --keyserver pgp.mit.edu --recv-key 8DD1BDFD

    gpg: requesting key 8DD1BDFD from hkp server pgp.mit.edu
    gpg: key 8DD1BDFD: public key "Sonatype, Inc. (Sonatype release key) <dev@sonatype.com>" imported
    gpg: no ultimately trusted keys found
    gpg: Total number processed: 1
    gpg:               imported: 1

We can make our lives even easier (depending on your tolerance for long commandlines...) by telling gpg to find and import keys automatically as they are referenced in the signature. Note that the following command combines the previous two steps into one:

    $ gpg --auto-key-locate keyserver --keyserver pgp.mit.edu --keyserver-options auto-key-retrieve --verify plexus-cipher-1.7.jar.asc ~/.m2/.../plexus-chipher-1.7.jar

    gpg: Signature made Tue Jul 26 20:06:33 2011 CEST using DSA key ID 8DD1BDFD
    gpg: requesting key 8DD1BDFD from hkp server pgp.mit.edu
    gpg: key 8DD1BDFD: public key "Sonatype, Inc. (Sonatype release key) <dev@sonatype.com>" imported
    gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
    gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
    gpg: Total number processed: 1
    gpg:               imported: 1
    gpg: Good signature from "Sonatype, Inc. (Sonatype release key) <dev@sonatype.com>"
    gpg: WARNING: This key is not certified with a trusted signature!
    gpg:          There is no indication that the signature belongs to the owner.
    Primary key fingerprint: 2BCB DD0F 23EA 1CAF CC11  D486 0374 CF2E 8DD1 BDFD

Our long-winded command rewards us with an even longer output, telling that this is a 'good signature'. Victory! Congratulations, you're now part of a select elite who actually verifies what they download from the internet before putting it into their mission-critical application:
[![Minority downloads sigs](/pics/tweet_jason.png)](https://twitter.com/jvanzyl/status/212890726386774017)

### Web of trust 
But all is not well. In case you missed it, there's a huge warning in the verification output, ending with 'There is no indication that the signature belongs to the owner'. This means that even though everything checks out, we can not be certain that the key is actually from 'Sonatype, Inc. (Sonatype release key) &lt;dev@sonatype.com&gt;'. Anyone could have uploaded such a key to the MIT keyserver. This is a hard problem&trade; to solve. With SSL certificates, the other well-known public/private key infrastructure, this conondrum is solved by having ultimately trusted root Certificate Authorities. This creates a centralized system which only works because browsers  distribute the trusted certificates of these selected root CAs to end-users.

PGP takes a different, decentralized approach known as the [web of trust](http://en.wikipedia.org/wiki/Web_of_trust). In essence, PGP allows public keys to be signed by other people. By doing so, they certify that that key is valid. This web of trust can work transitively. For example, if I sign person A's key, and person A signs person B's key, I transitively trust person B as well (there are different levels of trust, but I'll leave that aside for now). The idea is that we only sign another person's key if we met them in person and verified their identity. Obviously, it is benefial to have a large web of trust, to increase the chances that it contains a trusted someone close to yourself.

Let's try to visualize the situation in the case of Sonatype's release key:
![Web of trust](/pics/pgp_weboftrust.png)
Th red keys are private, the blue keys are public and arrows indicate signing. Three different individuals have signed the public Sonatype release with their private key. You can [lookup](http://pgp.mit.edu:11371/pks/lookup?op=vindex&search=0x0374CF2E8DD1BDFD) this information on the MIT keyserver. And, in turn, other people may have signed the public keys of these three individuals and so on. Now in order for gpg to fully trust the Sonatype release key, either we must sign it with our own key, or we must have signed any key that has a path in the web of trust to this release key. Signing the Sonatype release key directly would be the easiest, but not the most correct approach. How would we meet 'Sonatype, Inc.' and verify their identity? (hint: they might post their key fingerprint on a secure website). Let's assume instead that we know Brian F. Therefore, we can import and trust his key to put the web of trust in motion:

    gpg --keyserver pgp.mit.edu --recv-key 3C062231

    gpg: requesting key 3C062231 from hkp server pgp.mit.edu
    gpg: key 3C062231: public key "Brian E Fox <brianf@apache.org>" imported
    gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
    gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
    gpg: Total number processed: 1
    gpg:               imported: 1 

    gpg --edit-key 3C06223
    
    <snipped some certificate details>
    gpg> trust
    <snipped some certificate details>
    Please decide how far you trust this user to correctly verify other users' keys
    (by looking at passports, checking fingerprints from different sources, etc.)
    
      1 = I don't know or won't say
      2 = I do NOT trust
      3 = I trust marginally
      4 = I trust fully
      5 = I trust ultimately
      m = back to the main menu

    Your decision? 4

Instead of assigning trust to this key, we could have also signed it. Note that you need [your own keypair](http://www.gnupg.org/gph/en/manual.html#AEN26) to do this. Now lo and behold, no more warnings when verifying the signature:

    $ gpg --verify plexus-cipher-1.7.pom.asc  ~/.m2/.../plexus-cipher-1.7.pom
    
    gpg: Signature made Tue Jul 26 20:06:33 2011 CEST using DSA key ID 8DD1BDFD
    gpg: checking the trustdb
    gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
    gpg: depth: 0  valid:   1  signed:   1  trust: 0-, 0q, 0n, 0m, 0f, 1u
    gpg: depth: 1  valid:   1  signed:   1  trust: 0-, 0q, 0n, 0m, 1f, 0u
    gpg: depth: 2  valid:   1  signed:   0  trust: 1-, 0q, 0n, 0m, 0f, 0u
    gpg: Good signature from "Sonatype, Inc. (Sonatype release key) <dev@sonatype.com>"


### Automatic verification?
By now, you must be thinking 'that is an awful lot of work just to verify a single dependency'. And you're absolutely right. Applications typically use many dependencies, and checking them all by hand quickly becomes tedious. In my opinion, there is a huge opportunity for Maven-based build tools to support automatic PGP signature verification. Until that is the case though, you can also use Sonatype's Nexus repository manager as a proxy to Maven Central. It can automatically check the PGP signatures for proxied artifacts and refuse to serve them when the signature check fails. Unfortunately, this is only possible using the commercial version [Nexus Pro](http://www.sonatype.com/Products/Nexus-Professional), not with the [open source](http://www.sonatype.org/nexus/) version.

### Wrapping up
Before you dismiss everything above as wildly impractical, I urge you to read up on [cross-build injection attacks](/blog/security/2012/03/crossbuild-injection-how-safe-is-your-build). Decide for yourself whether your applications constitute a target for such attacks, and what the impact will be. If you'd like to hear more about this, I'm also giving a talk called 'Cross-build injections attacks: how safe is your Java build' ([CON3892](https://oracleus.activeevents.com/connect/sessionDetail.ww?SESSION_ID=3892)) at JavaOne 2012. May your build be secure!

