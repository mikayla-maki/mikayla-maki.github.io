---
title: "Drag and Drop Woes"
date: 2021-11-11T11:11:24-08:00
draft: false
---
Discord Transcript (future posts will be shared here first probably, maybe):

Working on a react-redux app, and I cannot use toolkit :(, and I'm having a lot of problem managing stale state in my app, but I think I have a solution. 

I have a trello clone and I need to implement drag and drop, this means I that I  have to have a virtual sort order for my lists and cards that isn't 'committed' to redux (and therefore saved to the browser) until after the drag and drop has ended. 

So far I've been using a single 'useSelector()' call in root that assembles the denormalized data and then passes it down to children in props (kinda, it's actually less organized than that but that's the gist) 

And I think that was a mistake.

What I'm going to change it to is officially start using selectors (defined in my slice files), each component is in charge of selecting and assembling it's little piece of state using the hooks 

And the only things passed through props are call backs and 'virtual' data, e.g. the on-hover sort order, or the currently selected board 

