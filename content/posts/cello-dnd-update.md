---
title: "Cello Dnd Update"
date: 2021-11-09T12:10:51-08:00
draft: false
---
Copy-and-Paste from Discord: 

I AM SO CLOSE

React DnD has been a tough library to learn, especially because I'm new to everything and regretting some earlier decisions 

BUT I am succeeding. I have Drag and Drop working (with bad UX) for cards in the same list, after much hair pulling.

Now I just have to re-do all that work, for the other two data sets, and then do something a little weird to enable cross-list dropping. 

It's actually kinda nice having three data types with the exact same dynamics, it allows me to easily DRY my code 

As I have three implementations of everything, enough to see the patterns and draw out the larger structures I'm playing with 

And then once this is all done, I'll finally be able to get back to why I actually started this project: playing with CRDTs and collaborative editing

Also, I have discovered that memo-ization has a secondary use beyond just caching: Dependency refreshing

This stuff is so cool

I just solved a gnarly seeming stale state bug simply by adding the misbehaving variable to the right memoization dependency 


