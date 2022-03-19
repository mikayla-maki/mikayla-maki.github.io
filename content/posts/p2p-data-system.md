---
title: "P2P Data System"
date: 2022-01-29T10:49:45-08:00
draft: true
---

Assumptions: [(what is this?)]({{<ref "posts/ms-idea-graph-format-specifier.md">}})

* Client-server architecture needs to be replaced by P2P

This is a follow up to my [previous post]({{<ref "posts/p2p-browser.md">}}), describing what I think a P2P based internet architecture would look like

Since my last post I got to bring this idea to some friends and colleagues and one of them
pointed me to a strikingly similar project called [Beaker Browser](https://beakerbrowser.com/). 
They used the exact same architecture; public keys as addresses, file server in the browser, and DHT based routing; that
I had envisioned. But their project died. It hasn't been updated in over a year. I believe it's because they failed at two
essential tasks: developers are unable to harness the power of a P2P swarm, and their UX failed to empower non-developers. 

P2P networks have a huge competitive advantage over traditional networks because of the enormous amounts of computation, bandwidth, and storage that can be mobilized for free. But these benefits come at the cost of synchronization complexity;
it's hard to get everyone on the same page at the same time. 
