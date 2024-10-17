---
layout: post
title: System Design
cover-img: ["assets/img/posts/Beach-Set.jpg"]
tags: [code, wip🚧]
---

A good overall guide is the [system design primer](https://github.com/donnemartin/system-design-primer/?tab=readme-ov-file) repo. (tinyurl, ...)
Also [gaurav sev]() has great videos like [horizontal vs vertical scaling](https://www.youtube.com/watch?v=xpDnVSmNFX0&list=PLMCXHnjXnTnvo6alSjVkgxV-VH6EPyvoX), sharding, ...

## Main concepts

System design contains ideas to solve answer questions. Usually there are different solutions that have different tradeoffs.

> Essentially what we do in programming all the time is transfering data in a nice manner. And to make things for efficient, we use caching and batching. And to make things resusable we try to make nice/smart APIs (but in practice your manager rarely things of that).
> We always think of what happens if a particular piece fails or takes too much time (timeouts, ...).

* "what type of database will I use when storing data?" Relational database (SQL databases)? or store database (NoSQL)? or niche databases (graph, timeseries)?
* "in distributed systems, how do I update replicas?" Availability vs consistency and CAP theorem. Weak/Eventual/Strong consistency.
* "how can I serve more clients as the traffic for my application gets bigger?" Horizontal vs Vertical Scaling
* "how is the network structured?" 7 layers, TCP/IP, UDP, CDN, packages and abstractions
* "how do individual programs (A and B) communicate (e.g. in microservices)? should A wait for B? should A continue? what if B stopped running? what if the network is down (and A cannot send a message)?" APIs, timeouts, clones, Push/Pull, Subscribe, HTTP (Rest APIs), WebSocket, clients and servers 

* Microservices vs Monoliths, network calls vs function calls
* Ways for services to communicate

And then you have all of these in practice:
* "how can I design a particular app? what individual pieces? how would they communicate? and why do I make these choices?"
* usually I assume some requirements (how many users?)
* "what kind of API would I provide to people?"

## More resources

So many online resources:
* [codekarle](https://www.youtube.com/@codeKarle/videos) has great videos like [buidling airbnb](https://www.youtube.com/watch?v=YyOXt2MEkv4), [tinyURL](https://www.youtube.com/watch?v=AVztRY77xxA&ab_channel=codeKarle&sttick=0), [uber](https://www.youtube.com/watch?v=Tp8kpMe-ZKw&ab_channel=codeKarle&sttick=0), [preparation series](https://www.youtube.com/watch?v=3loACSxowRU&list=PLhgw50vUymycJPN6ZbGTpVKAJ0cL4OEH3), [choosing sql vs nosql vs other dbs](https://www.youtube.com/watch?v=cODCpXtPHbQ&list=PLhgw50vUymycJPN6ZbGTpVKAJ0cL4OEH3&index=10&ab_channel=codeKarle). 

Specific videos:
* [How to build an exchange](https://www.youtube.com/watch?v=b1e4t2k2KJY&ab_channel=JaneStreet)
* [scaling dropbox](https://www.youtube.com/watch?v=PE4gwstWhmc&ab_channel=Stanford)
* [system design is a scam](https://www.youtube.com/watch?v=rKgtPABz9AY), obviously that is true. People design all these stuff throughout years. the answer is hybrid.

As for books
* System Design Interview, by Alex Xu is good for breadth
* Designing data intensive application is for depth (more hardcore)

As for system design patterns when coding (interfaces), [Refactoring Guru](https://refactoring.guru/design-patterns) is popular resource.
