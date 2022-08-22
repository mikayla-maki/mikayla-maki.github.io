---
title: "The Implications of the Grant Matcher Puzzle"
date: 2022-08-21T15:15:00-07:00
draft: false
---

I've been getting into Object Capabilites recently and it's been immense fun finding people solving all the same problems I've been thinking about for the last year. One of the more interesting topics is the 'Grant Matcher' puzzle and it's implications for what kinds of cooperation are possible. [Here's the original full text of the problem](http://www.erights.org/elib/equality/grant-matcher/index.html), but I'll be restating it below. 

### The Phantom Menace

First a quick Object Capabilities primer, if you know what OCap is just skip to the next section. Object Capabilities encode authority into object references. Having a reference to an Object is equivalent to having the authority to access that object in all ways that object allows. Contrast this with the more typical Access Control List model, where it's possible to maintain a reference to something but not have permissions to access it. It's a bit like the difference between file descriptors, and file names with permissions (which bundle in a concept of a system wide namespace). If you have a file descriptor, you know that someone, at some point, had the authority to open this file, and now you can do whatever this file descriptor is allowed to do (Append only, read only, read and write, etc.). Crucially, your code never had to worry about how this file descriptor is created, all of that complexity is encapsulated by the existence of the file descriptor. Contrast this with handing around file names. Names require that all parts of your codebase share the same file namespace (unlikely in distributed systems) and the simple existence of a filename doesn't provide any information about whether this code can (or should) use it. Using file names instead of descriptors breaks encapsulation by forcing all of your code to deal this complexity, every time. 

One of the core benefits of capabilities, and the one relevant to the Grant Matcher, is that they can be composed with Objects to create new behavior. One way this can happen is attentuation, making a capability 'smaller' before handing it on. Taking the file descriptor example above, imagine being given a 'read-write' file descriptor, and being able to wrap it in a 'read-only' handle that you then pass to another part of the system. Another way is through transparent references, which allow you to augment your existing capabilities with new behaviors. Again with file descriptors, it's like being able to wrap a file descriptor with a boolean that revokes access to the file, without touching the underlying OS file descriptor, while still appearing to be a plain old file descriptor to whoever you're handing it to. The Grant Matcher puzzle puts these benefits to the test.
 
 ### Attack of the Grant Matcher Puzzle 

Let's imagine a world with the following Objects:

- A Charity
- Alice
- Bob
- A Grant Matcher

The Grant Matcher's job is simple: Check if both Alice and Bob donated $10 to it. If they both did, send their combined money ($20) to the Charity. If they didn't, refund their money. Alice and Bob do not trust each other but they do trust the Grant Matcher to perform it's duties. Finally, Alice and Bob both have a reference to the Grant Matcher and a reference to the charity. Here's a diagram of the setup:


{{< figure src="/grant-matcher/initial.png" caption="Black, thin arrows denote references, circles denote objects. Access is authority in Object Capabilities, so both Alice and Bob can talk to the grant matcher and the Charity, but not to each other." >}}

 Here's how it should go, Alice and Bob send the Grant Matcher their cash, the grant matcher checks that their donations match, and the grant matcher sends their money on:

{{< figure src="/grant-matcher/success.png" caption="First, Alice sends $10 to the Grant Matcher. Then Bob sends $10 to the Grant Matcher. Then the Grant Matcher donates their $20 to the Charity" >}}

But there's a problem with this picture: messages can only be sent when there's a reference to an Object, and according to the setup to this puzzle the Grant Matcher doesn't have a reference to the Charity. Let's fix this by adding references to the messages that Alice and Bob send:

{{< figure src="/grant-matcher/refs.png" caption="First, Alice now sends $10 to the Grant Matcher and a reference to the charity. Then Bob sends $10 to the Grant Matcher and a reference to the charity." >}}

But here's the crux of the problem: how does the grant matcher know if these references are equal? To preserve referential transparency, the Grant Matcher must ask both references for their identity. Alice can use this fact to insert a reference to a Fake Charity:

{{< figure src="/grant-matcher/fake-attack.png" caption="Alice now sends $10 to the Grant Matcher and a reference to a Fake Charity object. Then Bob sends $10 to the Grant Matcher and a reference to the real charity. The grant matcher then sends a message to both the fake and real charity asking for their identity. The fake charity forwards this message on to the real Charity" >}}

When the Grant Matcher tries to ask for the charity ID, Alice's Fake Charity can forward the message on to the real charity, and then return the results to the grant matcher:

{{< figure src="/grant-matcher/message-forwarding.png" caption="The same image as before, but now annotated with the response from the Charity, 'KEQD', going to both the Grant Matcher and the Fake Charity. The Fake Charity then returns the same identifier, 'KEQD', to the Grant Matcher. The grant matcher then thinks 'Both references say they're the same thing, great! These references must match.'" >}}

The grant matcher then picks Alice's reference to send the donation on and Alice's fake charity sees that this message has the money on it, and instead sends it to Alice!

{{< figure src="/grant-matcher/fake-steals.png" caption="The grant matcher sends a message to donate $20 to Alice's fake charity, and the fake charity forwards that Message to Alice, successfully stealing Bob's money" >}}

So we're at an impasse. Capabilities require referential transparency to provide their value, but referential transparency creates a serious security problem. 

### Revenge of the Reference Equality Operator

From what I've learned, there are two standard solutions to this problem: abandon referential transparency and use reference equality check and use a sealer/unsealer pair. 

Reference equality requires that Bob and Alice have the exact same reference, and thus the exact same authority, to the Charity in order to use the Grant Matcher. This is a massive restriction and just pushes the problem onto Alice and Bob to solve, how are they going to agree on which of their two references to use if they don't trust each other? Note that this also requires Alice and Bob to somehow know that they have the *exact same* reference, either by communicating or in the puzzle setup.

The other solution is to use a sealer where the charity holds the corresponding unsealer. Sealers allow people to put something in a box that only the unsealer can open. If both Alice and Bob seal their money with the Charity's sealer, then there's no point to stealing as Alice couldn't retrieve the money even if she did pull off a successful attack. Note that this doesn't actually solve the grant matcher puzzle either, Bob still lost his money, it just takes away Alice's incentive to steal. Note that this requires trust in the Charity's behavior.

### A New Hope

So now that we've seen the problem and the solutions, let's take a step back, why does this problem exist at all? I propose that, in the setup of the Grant Matcher puzzle, there is a violation of the Principle of Least Authority that causes all of these issues. Let's go back to that successful grant match diagram:

{{< figure src="/grant-matcher/success.png" caption="First, Alice sends $10 to the Grant Matcher. Then Bob sends $10 to the Grant Matcher. Then the Grant Matcher donates their $20 to the Charity" >}}

The problem with this situation was that, according to the puzzle setup, the Grant Matcher doesn't know about the Charity. Because it doesn't know about the charity, the Grant Matcher must be given both the authority to check matching donations, as well as the authority to resolve the two references into a single 'true reference'. It's this second authority which breaks encapsulation, forces the Grant Matcher to use a raw reference equality check, and make Alice and Bob unable to cooperate safely.

So let's break that second authority out and see what happens. In order for the Grant Matcher to know where to donate the money, it needs to be created with a trusted reference to the correct Charity to donate to. If Alice and Bob don't trust each other, or each other's references, the only entity which can provide a trusted reference to the Charity is the Charity itself. This requires that Alice and Bob trust the Charity to behave correctly. Note that the original puzzle statement explicitly removes the possibility of trusting the Charity:

>"The Grant Matcher pattern, however, is supposed to bring about a particular kind of cooperation between Alice and Dana, requiring **only** that they both trust the Grant Matcher and a common monetary system." Emphasis added. [From erights.org](http://www.erights.org/elib/equality/grant-matcher/index.html)
    
However, the sealer/unsealer pair solution to the Grant Matcher puzzle requires a trustworthy Charity, and further I can't conceive of a scenario where Alice and Bob both trust the Charity with their money, but don't trust it to behave correctly (ignoring technical glitches). Given these two statements, I consider adding a trustworthy Charity to the initial conditions to be an acceptable modification to the rules of the puzzle. 

Back to the puzzle. Intially there are only Alice, Bob, and a Charity. Alice and Bob use their references to ask the Charity to create a grant matcher, it gives them references to a Grant Matcher that the Charity has created. This Grant Matcher contains a reference back to the Charity:

{{< figure src="/grant-matcher/new-initial.png" caption="In this new initial state, Alice and Bob both ask the Charity for a new Grant Matcher. The Charity instantiates a Grant Matcher with a reference to the charity, or a 'Charity Matcher'" >}}
 
 Now there isn't even a puzzle. Alice and Bob can't forward references to the Grant Matcher (it only cares about their money) and so Alice can't launch her attack on the Grant Matcher. This also preserves referential transparency, as Alice and Bob's references to the Charity, and their references to the Grant Matcher, can be behind as many different kinds of references as they please. All that matters is that the Charity is capable of receiving Alice and Bob's messages, is capable of giving them a reference to the new grant matcher, and the Grant Matcher is eventually capable of being called twice with the money.
 
 Success! We've eliminated reference equality checks! Or have we?
 
 ### The Reference Equality Operator Strikes Back 
  
 An issue with the example above is that the Charity must somehow know who to send which references. We can imagine a scenario where there are thousands of people all simultaneously starting and resolving Grant Matcher sessions on this Charity. The Charity will need some identifier associated with both Alice and Bob which uniquely identifies their session. There are at least two different ways this could work:
 
 1. Name-based. Alice provides a name for Bob when requesting a session: 'Start Grant Matcher with Bob, from Alice'. Alice then tells Bob, through a third party, that she used the name 'Alice' for herself and 'Bob' for him. Bob then sends the reverse message: 'Start Grant Matcher with Alice, from Bob'. 
 
 2. ID-based. Alice sends a 'Start Grant Matcher Session' message to the charity. The charity returns a session ID to Alice. Alice hands this session ID to Bob and Bob sends a 'Join Grant Matcher Session with ID' to the charity. 
 
The name based solution does not require communication, but does require out of band information. Alice must be able to refer to Bob, and Bob to Alice, without actually having a proper reference to each other. They do this by falling back onto a global namespace of human-readable names produced from somewhere outside of our system of Objects and references. This requires bundling in all of the complexity of matching human names into the Charity. These human-readable names can be thought of as a 'raw reference', something carrying the capacity to refer without all of the behavior of a proper OCap reference.

The ID based solution requires Bob and Alice to be in some kind of communication, but does not require any out of band information or even an external namespace. The Charity encapsulates every possible session ID and so can do a fast and cheap bitwise comparison. If we allow Alice and Bob's communication to be in-band via normal OCap references, we can even hide the existence of these session IDs entirely, with a protocol like so:

 {{< figure src="/grant-matcher/non-raw-reference.png" caption="A diagram of the 4 states of the system between Alice initiating a grant matcher session and Bob joining it. Alice and Bob both have a reference to Charity and Alice has a Reference to Bob. Alice asks the charity for a session key. Alice receives a promise for a Grant Matcher and a reference to the session key. Alice then sends a reference to this session key to Bob. Bob redeems his session key with the Charity for a reference the now resolved Grant Matcher." >}}

 ### The Return of Reference Equality
 
  All of this is to show that the Grant Matcher puzzle is impossible to solve without loosening one of the puzzle's constraints. Once the Grant Matcher Object is adjusted to reduce it's authority, the puzzle is revealed to really be asking Alice and Bob to refer to each other without actually having a reference to each other. This violates the entire premise of OCap but is quite common in the real world. Imagine Bob and Alice met on twitter, and want to match their grants, but their OCap systems have never communicated before. Alice and Bob need to start by establishing a mutually trusted namespace and exchange raw references within this namespace, before they can cooperate at all. Thankfully, OCap is more than capable of modeling this complexity. As the session keys example shows, we can have our referential transparency cake and eat our reference equality too. We may never be rid of *a* reference equality operator, but at least we don't need to use *the* reference equality operator to cooperate.
