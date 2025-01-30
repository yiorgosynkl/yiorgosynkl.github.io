---
layout: post
title: System Design
cover-img: ["assets/img/posts/Beach-Set.jpg"]
tags: [💻code, 🚧wip]
---

A good overall guide is the [system design primer](https://github.com/donnemartin/system-design-primer/?tab=readme-ov-file) repo. (tinyurl, ...)
Also [gaurav sev](https://www.youtube.com/@gkcs) has great videos like [horizontal vs vertical scaling](https://youtu.be/xpDnVSmNFX0) (and sharding, ...). This article contains [18 fundamental concepts for system design](https://www.designgurus.io/blog/system-design-interview-fundamentals). The OG book about to go deeper is [DDIA](https://www.oreilly.com/library/view/designing-data-intensive-applications/9781491903063/).


## Main concepts

System design contains ideas to solve answer questions. Usually there are different solutions that have different tradeoffs.

> Essentially what we do in programming all the time is transfering data in a nice manner. And to make things for efficient, we use caching and batching. And to make things resusable we try to make nice/smart APIs (but in practice your manager rarely things of that).
> We always think of what happens if a particular piece fails or takes too much time (timeouts, ...).

A list of questions and related important topics to know:
* "what type of database will I use when storing data?" 
    * Relational database (SQL databases)? or store database (NoSQL)? or niche databases (graph, timeseries, vector)?
    * ACID principles
    * Data Partitioning (sharding) (horizontal vs vertical)
    * Database Index
* "what happens if a particular part of the system crashes?" and "what happens when the system goes back up? what data were lost?"
    * Difference between database (disk) and memory (RAM)
    * Persistency of data, losing in memory, picking up from last checkpoint
* "what are common tradeoffs to consider for system design?", "how do I choose between tradeoffs depending on my project?"
    * Performance vs scalability <details><summary><i>(Explanation)</i></summary>If you have a performance problem, your system is slow for a single user. If you have a scalability problem, your system is fast for a single user but slow under heavy load.</details>
    * Latency vs throughput, Availability vs consistency (CAP theorem)
* "in distributed systems, how do I update replicas?"
    * Database Replication for enhancing reliability
    * Availability vs consistency (CAP theorem). Weak vs Eventual vs Strong consistency
* "how can I serve more clients as the traffic for my application gets bigger?" 
    * Horizontal vs Vertical Scaling
    * Load Balancers (Round Robin, Least Connections, IP Hash)
* "How can I increase performance for an existing system?"
    * Cache (Client / CDN / Web Server / Database / Application caching)
    * types of caching (write through / Cache-aside / Write-behind (write-back) / Refresh-ahead )
* "how is the network structured?" 
    * OSI model (7 layers), TCP/IP, UDP, CDN, packages and abstractions
* "how do individual programs (A and B) communicate (e.g. in microservices)? should A wait for B? should A continue? what if B stopped running? what if the network is down (and A cannot send a message)?" 
    * Asynchrous vs Synchronous systems (Message/Task Queues, publish/subscribe)
    * Popular Message Queues (Apache Kafka, RabbitMQ, Redis (not persistant))
    * APIs, clients and servers, timeouts, Push/Pull, Publish/Subscribe, HTTP (Restful APIs), WebSocket, 
    * Microservices vs Monoliths (network calls vs function calls)
* "how do I estimate requirements fast to decide between tradeoffs?"
    * e.g. "amount of users", "request per day", ...
    * Back-of-the-envelope calculations

And then you have all of these in practice:
* "how can I design the system for an app idea? what are individual pieces and how would they communicate? what are the assumptions about users/data/requests/...? what functionalities to a provide to solve the users problem? what will the API/interface be and how does it affect the system design?"

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
