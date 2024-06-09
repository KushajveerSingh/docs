---
title: Distributed Computing Course by Chris Colohan
---

Examples of what you could work on

-   build a multi-user chat system
-   build a data analysis using Hadoop
-   attempt to understand Paxos and build your own implementation

---

Resources

-   https://www.distributedsystemscourse.com/

Distributed vs centralized system

-   **Centralized system** - State stored on a single computer.
-   **Distributed system** - State divided over multiple computers.

Why build a distributed system?

-   **Legal/privacy/politics** - Tor is a distributed system, not for performance reasons, but to make it harder to identify the identity of the user.
-   **Aligning cost incentives** - Users can provide compute, so that it is easier for the company.
-   **Uptime requirements** - Some components in the system might have more strict uptime requirements, and thus have additional hardware. While the other components are considered window decorations, and can run on a single computer.
-   **Performance (latency/bandwidth)** - Having a separate server in New York and China is much better for performance. CDN is a similar example of this.
-   **Dependency on cloud** - For situations when state is divided between cloud and local system.
-   **Reliability** - Even at infinite cost, a single server can only be made reliable up to a fixed amount.
-   **Scaling** - Vertical scaling (using better CPU), can provide only so much benefit. In certain situations, doing horizontal scaling can provide more value for money. People generally overestimate the scale of their product.

Topics in distribted systems

-   How systems fail.
-   How to express goals i.e. communicate to users that the system is reliable
    -   SLIs (Service Level Indicator) - Pick what you want to measure, to indicate the reliability of your system. And also, identify the technique for measuring them.
    -   SLOs (Service Level Objective) - Pick how good you want the chosen SLIs to be. E.g. 99% success rate of requests in 1 hour.
    -   SLAs (Service Level Agreement) - SLO + consequences, and this is what the customer is interested in. Like if 99% success response is not met, then the company will pay $1 fee.
-   How to combine unreliable components to make a more reliable system. There are infinite ways to combine components, but using common patterns is helpful.
-   How nodes communicate.
    -   Shared memory model. Not used much in distributed systems.
    -   Message passing model, implemented using Remote Procedure Calls (RPCs).
-   How nodes find each other.
-   How to use time in a distributed world. It is important to preserve order of events on multiple machines. We want to sync the clocks as close as to each other, to have higher resolution, than the things you are trying to differentiate.

    Since it's a hard problem, theorists have come up with the concept of virtual clocks, which focuses more on the relationship between events.

-   How to get agreement (consensus). If anything happens, like a node fails, network fails, you need a way for the system to recover and agree upon what happened.

    Paxos is the gold-standard algorithm for this i.e. providing agreement on the order of events and storing them in a durable storage.

    Paxos is really hard to understand, and that is where Raft comes in. It is as good as Paxos, but easier to understand.

-   How to persist data (distributed storage). Storage systems are the most frequent components to fail, and for this reason most of the research in distributed systems comes from storage researchers.
-   How to secure your system.
-   How to operate your distributed system (Site Reliability Enginerring, SRE).

Types of failures

-   Process crashes or ends in a crashloop (where it comes back, and crashes again, and so on).
-   Data corruption. You might be reading incorrect data from disk.
-   Server down.
-   Query of death, where a user discovers a query that can take down your system, and they repeatedly keep sending that to your server.
-   Broken dependency. A service you depend on, gives you an incorrect response.
-   Denial of Service attack. And this can also include valid requests, it's just that your server was not expecting the load.
-   Cascading failure. One server goes down, and that causes the next server to go down.
-   Heisenbug, where you cannot replicate the failure in debugging and testing environment.
-   Customer reported failure, but it cannot be replicated by you.
-   Data loss.
-   Time travel, where the time your server depends on, happens to have gone backwards.
-   Security breach, where a single node or the entire system is compromised.
-   Natural disaster.
-   Operator error, where someone with root access took the system down.
-   Runaway automation, where root user automates something, and that causes failure.
-   Certificate expires.
-   Kernel memory leak.
-   Isolation failure, where your process is running alongside other processes, and their process is causing you performance issues.
-   Hash collision, when you are using a large amount of data.
-   Incorrect algorithm, an algorithm you trust for years, had a hidden error.

Split failures into categories based on importance (frequency \* impact).

First category: Network failure vs Node failure

Network failure

-   The following failures happen lose packets, corruption, routing issues, multipath issues, congestion collapse.
-   Network engineers gave us an abstraction, that either data goes from one computer to another, or it does not. This is TCP/IP.
-   TCP/IP does not handle security, this is where you add SSH, which provides an encrypted tunnel between any two nodes.
-   The two things that the abstraction does not provide us is (these need to be handled by the application developer)
    -   Loss of connectivity.
    -   Network not fast enough (or remote node is too slow).

Loss of connectivity can happen, when a node goes down, and this results in the network graph being partioned into two subgraphs, with no way of communicating in between. (If the entire system is used for readonly requests, then this is not an issue, as the state never changes).

The main problem is when two nodes write to same data item in different subgraphs. This can result in lost consistency (the global state diverges).

**CAP (Consitency Availability Partitioning) principle** - A simple solution, is to detect partition, and disable nodes in all but one subgraph, until the partition heals. This is called tolerating an inconsistent state, which can be hard/easy depending upon your application. This comes at the cost of availability.

There is a trade off between consitency and availability.

Consistency example. If you are on Facebook, and interact with a post on node A, and another post on node B. Now when you add a comment on node A, that comment should also be accessible when someone uses node B to view that post. If the nodes are partitioned, they would not be able to propagate these comments.

How to detect partitions (Quorum algorithm)

1. Each node periodically pinhs all the N nodes.
2. Count the number of responses (R) received back.
3. If R <= floor(N/2), then the node switches to read only mode and stay in this safe state until the network heals itself.

The downside of the algorithm, is when the graph is partitioned into 3 subgraphs, then all the nodes will halt.

Node failure

-   Fail stop (easier to resolve). Node stops working like software crash, power outage, hardware failure, out of memory/disk full.

Strategies to deal with it

-   Checkpoint state and restart (high latency) - Restart the node, and load the last good data that you were working on. Does not handle hardware failure.
-   Replicate state and fail over (high cost) (superior) - Fail over to another node. Periodically save state to more than one node, and use a load balancer to redirect to the new node.

Node failure

-   Byzantine failure. Everything that can go wrong, which does not cause the node to stop.
-   Example
    -   Bit flip in memory or on disk corrupts data.
    -   Older version of code on one node sends (now) invalid messages.
    -   Node starts running malicious version of software.
-   Theoretically, you can resolve a couple of byzantine failures, but more is impossible to recover.
-   Since resolving byzantine failure is expensive, and can cause the system to slow down, its better to monitor the most frequent errors, and convert them into fail stop errors.
    -   For bit flip, put checksums on all data structures when writing to memory, and verifying the checksums, when reading the data again. If the checksum fails, you treat it as fail stop error, and shut down the node, and do the appropriate recovery.
    -   You can write assertions in code, and when the assertions fail, you can fail the program and do the appropriate recovery.
    -   Another thing to keep in mind is the error can occur with multiple nodes at the same time, and shutting down all the nodes at the same time can cause cascading failure. So you can look into timeouts, and ensure that not all nodes fail at the same time.

For network, you worry about partitions. Network protocol handles the rest.

For node byzantine, attempt to transform into fail stop.

For node fail stop, checkpoint and recover, or replicate and fail over.

Byzantine Fault Tolerance

https://www.distributedsystemscourse.com/
https://www.youtube.com/watch?v=_e4wNoTV3Gw&list=PLOE1GTZ5ouRPbpTnrZ3Wqjamfwn_Q5Y9A&index=6
