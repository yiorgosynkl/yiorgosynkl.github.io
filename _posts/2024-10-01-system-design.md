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
    * Performance vs scalability <details>If you have a performance problem, your system is slow for a single user. If you have a scalability problem, your system is fast for a single user but slow under heavy load.</details>
    * Latency vs throughput <details>Latency is the time to perform some action or to produce some result. Throughput is the number of such actions or results per unit of time. Generally, you should aim for maximal throughput with acceptable latency.</details>
    * Availability vs consistency (CAP theorem) <details> **Consistency** (every read receives the most recent write or an error) & **Availability** (every request receives a response, without guarantee that it contains the most recent version of the information) & **Partition Tolerance** (The system continues to operate despite arbitrary partitioning due to network failures). CAP theorem proves that you can have only 2 out of 3. Furthermore, networks aren't reliable by default, so you'll need to support partition tolerance. Therefore, the software tradeoff choice is between consistency (CP system - Waiting for a response from the partitioned node might result in a timeout error. CP is a good choice if your business needs require atomic reads and writes, e.g. a bank withdrawal system) and availability (AP system - Responses return the most readily available version of the data available on any node, which might not be the latest. Writes might take some time to propagate when the partition is resolved. AP is a good choice if the business needs to allow for eventual consistency or when the system needs to continue working despite external errors, e.g. Twitter/Facebook where people will eventually see the updated version of your profile ) </details>
* "in distributed systems, how do I update replicas?"
    * Database Replication for enhancing reliability
    * Availability vs consistency (CAP theorem). 
    * Consistency (Weak/Eventual/Strong): With multiple copies of the same data, we are faced with options on how to synchronize them so clients have a consistent view of the data. <details>**Weak consistency**: After a write, reads may or may not see it. A best effort approach is taken, works well in real time use cases such as VoIP, video chat, and realtime multiplayer games (memcached). (e.g. if you are on a phone call and lose reception for a few seconds, when you regain connection you do not hear what was spoken during connection loss). **Eventual consistency**: After a write, reads will eventually see it (typically within milliseconds). Data is replicated asynchronously (e.g. systems such as DNS and email. Eventual consistency works well in highly available systems). **Strong consistency**: After a write, reads will see it. Data is replicated synchronously (e.g. file systems and RDBMSes).</details>
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


## important distinction

In broad there are 3 types of systems:
* online systems (services): it's the most popular in the web and uses the request/response paradigm. the client makes a request, the server gives a response and the communication is synchronous. The client also sets a timeout (the maximum time the client will wait for a response, if no response arrives by the timeout time). The primary measure of performance is response time (be fast) and availability (answer always and don't time out).
* offline systems (batch-processing): a batch processing system takes a large amount of input data, runs a job to process and produces a result. usually there is no user waiting for a response during that time. the primary measure of performance is throughput (process a lot very fast, that's why you aggregated the whole batch).
* near-real-time systems (stream-processing): it's something between online and offline. A stream processor consumes inputs and produces outputs, like batch-processor, but does shortly after a request was made, like a service. The important metrics are both latency and throughtput.


## Stream processing / Message brokers

In batch processing, we get all the data first (the input is bounded), then execute a job for them. In reality, a lot of data are gradually available over time (e.g. the users execute new actions every day). We don't want to wait a whole day or even hour to process something. 

Stream refers to data that are incrementally available over time. The concept appears also in Unix terminals (stdin/stdout), TCP connections, delivering audio/video, filesystems and programming languages (lazy lists, etc).

Event streams.


Message Brokers is a general concept in software system design. They're systems that handle passing messages between different parts of your system. 
Some popular solutions are Kafka, Apache Flink, RabbitMQ, Redis (with multiple possible configurations and different pros/cons).

The two main properties we are concerned with are **persistence** (also known as durability) and **ordering**. 

Persistence spectrum ("if the machine crashes, will I lose data?"):
* Purely in-memory (lost on restart)
* In-memory with periodic snapshots
* In-memory with WAL (write-ahead logging)
* Fully persistent with immediate disk writes
* Replicated persistence across multiple nodes

Snapshots write the entire queue state (take a "photo") in disk periodically (e.g., every 5 minutes). If the system crashes, you lose all messages since the last snapshot.
It's faster than WAL since disk writes are batched and take more disk space per write since you're writing the full state. Recovery after crash is simple: just load the last snapshot

Write-Ahead Logs record every operation (enqueue/dequeue) before performing it and write continuously as messages arrive. If system crashes, you lose at most a few milliseconds of operations, but it's slower than snapshots during due to frequent disk writes.
More space-efficient since you only write the changes but recovery takes longer: must replay all operations since start.

> A log can be thought simply as an append-only sequence of records on disk. 

Most queues have a hybrid 
Most systems use a hybrid approach of snapshots and WAL. They use WAL for recent operations and take periodic snapshots, after which they can truncate the WAL before the snapshot. On recovery, load the last snapshot then replay WAL from that point.

> Remember that disk writes are slow and want to avoid them (but they are also necessary because the provide persistence).

Ordering spectrum ("are the messages processed in the order that they are being published?"):
* No ordering guarantees
* Partial ordering (e.g., within a partition/shard only)
* Total ordering within a queue
* Global ordering across all queues in the system

> The key trade-off is between strict ordering and throughput. 

If we have total ordering in a queue, we have to make sure we process the first message before we process the next one (which prohibits us from having multiple consumers on that queue). In reality, we usually use partial ordering, aka we split our data in multiple ordered partitions and have one consumer per partition. And we make sure to route ordered dependent data in the same queue (e.g. the transactions of a specific user go always in the same queue).

A problem arises when a consumer crashes while processing a message from a queue. From the queues perspective, it sees a consumer crashing but is uncertain as to wether it crashed before or after completing the processing of the message (<details>The consumer may crashed before completing processing and no results were propagated to other services. Or the consumer may completed processing and propagated results to other services but crashed before letting the message broker know.</details>). Here, we have the concept of delivery gurantees:
* at-least-once (most popular usecase) delivery guarantee: if the consumer crashes during processing, we consider the message unprocessed and replay processing. 
* at-most-once (also known as "best-effort") delivery guarantee: if the consumer crashes during processing, we consider the message unprocessed and replay processing.
* exactly-once: using an (idempotency) key associated with each message. When consumer completes processing of message, it saves the key to disk (persistant storage) and propagates results to other services (in an atomic operation, either all or none happen). therefore, we can always check the db to ensure if we processed the message or not. the drawbacks are extra latency (because of writing in disk, which is slow) and extra space for storing keys.

Other keywords:
* fan-out: one message from one producer is delivered to multiple consumers (e.g publishing a post in twitter)
* fan-in "sink": multiple messages from producers is delivered to one consumer ()
* these two can work together, e.g. when uploading one video in YT, we have to do multiple jobs (fan out to multiple consumer, like encode in 720 resolution, 1080 resolution, save thumbnail, ) and then we want to know about completion to notify user (fan in to one consumer all the jobs are done).

## More resources

This [video of 1 hour system design](https://youtu.be/iYIjJ7utdDI) explains the main patterns asked in interviews: 
* Contending updates (many writes for the same key, locking the db will make it slow). Optimise with multiple write leaders. Or stream processing and batching writes (therefore less writes and congestion).
* Derived data (keeping datasets in sync).
* Fan out (e.g. sending push notificaiton or news feed updates). Asynchronously doing work instead of sending requests to thousand of destinations. Essentially sink all messages to a log-based message broker (e.g. Kafka) and consumer logic will handle finding the appropriate parties to send. Caveat the "popular message" or "hotkey". Use popular message cache were users poll for these (instead of push notifications going to everybody).
* Proximity Search (find close items in db, e.g Uber, Lyft). Indexing on latitude and longtitude is the naive solution. Better to use geospatial indexing (and sharding because of huge amount of data).
* Job Scheduling: run a series of job (e.g. build a scheduler or youtube saving video in different encodings/resolutions). We don't care where the tasks run, we use in memory message broker and round robin to workers. No time dependency in messages (aka requirement to process messages in order) therefore we do not need a log-based message broker (if a partition worker is slow, the rest of the workers in the partition have to wait for the slow worker, which is inefficient).
* Aggregation: distributed messages need to be aggregated by some key/time (e.g metrics). Naive solution to store in database and run jobs afterwards. The faster solution is stream processing (with log-based message broker) and partitioning based on the aforementioned key (therefore the proper process will be handling all the key data).
* Idempotence: you don't want to see the result more than once (e.g. amazon confirmation emails in orders). Naive solution is Two-phase commit in dbs. The smart solution is using idempotency key, storing a unique ID for each job (e.g save them in DB) (if we see again the same ID, that means we already processed and send the relevant message). If this happens on the consumer (instead of the producer of messages), it's called "fencing".
* Durable data: data that absolutely cannot be lost once written (e.g. financial applications). Synchronous replication is the first solution (guaranteed to propagate writes to all dbs, but if one replica goes down, no writes can be made, so no fault tolerance). Therefore, the practical solution is a distributed consensus algorithm (and using a log that we know persists).  

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
