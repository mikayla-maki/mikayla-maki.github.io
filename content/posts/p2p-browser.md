---
title: "P2P Browser"
date: 2022-01-22T15:04:11-08:00
draft: false
---

Assumptions: [(what is this?)]({{<ref "posts/ms-idea-graph-format-specifier.md">}})

* Client-server architecture needs to be replaced by P2P

I've been seeing a [lot](https://twitter.com/illegally_beka/status/1480286112299962370) 
[of](https://www.inkandswitch.com/local-first/) 
[talk](https://twitter.com/rechelon/status/1484423600933380098?s=20) from smart people about how to do a p2p internet
properly, and I want to throw my own architecture proposal into the ring. My goal is to surpass the ease of use and
accessibility of modern social media apps, while also structurally distributing
power to users (also referred to as 'peers').

There are some patterns of problems that I see in these (and other) discussions:

* Identity needs to be handled off-app. Twitter should not own my identity token, and neither should 
  anyone else. Once identity is freed from the corporate ecosystems, we can create all kinds of communal
  filtering and processing functionality on a per-peer basis. But we have to get it out first.
* Virtual networks should be effortless to create and switch with no personal data lost.
* Bits need to be on hard drives. If they're not on your personal phone, laptop, or server, then they're
  not really yours.
* All of this, and it needs to run small enough and fast enough that any android phone can be a fully
  functional peer.

Identity is the trickiest to think about, as everyone is concerned about controlling who has access to what
identity, but I think this is actually the most trivial to solve. When I'm browsing the internet I don't care
who anyone 'really' is, all I care about is that I can identify their content. Usually they help me figure it
out by linking their blog and building their reputation. Therefore, the only component you need for a fully functional
identity system in a peer to peer context, is an asymmetric key generator. Asymmetric key generators allows you create a public
and private key pair. Anyone who has the public key can verify that the private key was used to sign a message. This fulfills
the reputation function that lies at the core of human to human identity. Using a generic public key generator as the atomic
identity source also gives you features like alt accounts and identity changes for free, which I really appreciate as a 
trans person.

The virtual networks point is the one I have the least understanding about. [This @rechelon thread has some interesting critiques 
of modern networking](https://twitter.com/rechelon/status/1484423600933380098?s=20). My lack of understanding tends to
drive me towards abstracting the underlying network topology, specifically for the purposes of replacing it at some later date. 
But I am unsure if that is falling into the exact problem described above. Regardless! My thinking is that networks are fundamentally zones of awareness.
Any node in a network can talk to the other nodes in the network and can't really talk to nodes outside the network
(unless they're a special node who is also on an internetwork network). The peer to peer way to create this mutual awareness over an 
arbitrary underlying topology is with a Distributed Hash Table (DHT). This DHT would take public keys and return (signed) addresses
where the public key holder can be found. One of the goals is to make network creation and switching effortless, so new DHTs 
should be easy to join (via an invite link to solve the initial-peer discovery problem) and the software should enable the same
machine to be a part of dozens of networks. This allows people a high degree of choice in how visible they are to others, even 
before activating the public key filtering mechanisms. And since public keys and networks are disconnected, filtering by public key works
across networks by default. 

Right now, these DHTs would probably return IP addresses, but any address that the client understands how to communicate with
should theoretically work. This brings us to the third point, bit localization. I believe that the DHT should serve the (signed) address
to a file server that provides (signed) data relating to the given public key. This file server should support push and pull
dynamics, e.g. subscriptions and requests and such, as the data on it is created and changed. I personally think the 
fundamental file type should be a CRDT of bit stream operations, but
I haven't gotten far enough into CRDT technology to tell yet. I call this component the 'datastore'.

And finally, the usability angle. This should all be done inside a browser, using a similar paradigm as the client side
single page web app. A P2P browser would come with the key generation, file serving, and DHT engine built in. DHT connections
would be initiated by a protocol mediated invite from the initial peer as well as any engine configuration data. A 
web app could then be deconstructed into three components: A UI, a file or directory format inside the datastore, and a sorting function over incoming
data streams. Making a new twitter UI is as trivial as swapping out the HTML and JavaScript template files. The browser can expose
an API to resolve public key references into actual data to minimize network chatter. Mesh network based
moderation can be done through browser plugins, that allow communities to build collective block lists or trust webs,
while also allowing any individuals to punch through the block lists whenever they want. It's their client. They can configure
and control it however they like. 

The final issue is scalability and intermittent/faulty connectivity of p2p systems. Unreliable clients can be
smoothed by augmenting the browser to also cache and serve any data it sees passing by. It's much easier to scale a viral
tweet if everyone retweeting it can also temporarily service requests for it. It would also be trivial to delegate file
serving capacities under this system, as data is only valid if signed, getting an always-on node might be as simple as
paying someone to host your data for you and issuing a signed IP address update to the DHT. If you don't like what they do,
it's fine, as the data is already replicated on your local browser. Simply stop sending it to them. Running out of local 
storage space can be solved in the same way, simply delete the data off of the browser; optionally backing it up first.
 There are many more potential problems waiting in the wings, but these seem to be no more difficult than the
problems already faced by much of the internet, except now people have the tools to solve them for themselves.

This is the sketch of the idea for how to restructure things while maintaining the performance and accessibility targets that 
are needed for this system to take match or best the current architecture.
Work needs to be done to create the browser described here as well as the generic DHT protocols and engine. I also
need to design and test an app under this paradigm and see if it works as well as it does in my head. Feedback, critique,
and links to related projects are highly appreciated. I'd hate to waste time on this if it turns out it's a bad idea.

