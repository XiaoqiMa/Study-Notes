### 2018.1.10

DHT: Distributed Hash Table; DHTs are called structtured P2P networks

Definition: 

> A distributed system is a collection of autonomous computing elements that appears to its users as a single coherent system

Characteristic 1: collection of autonomous computing elements

Distributed system is often organized as an *overlay network*. A well-konwn class of overlays is formed by peer-to-peer (P2P) networks. 

Characteristic 2: single coherent system

Distribution transparency is an important design goal of distributed systems. 

Communication A common communication service is the so-called Remote Procedure Call (RPC). An **RPC** service allows an application to invoke a function that is implemented and executed on a remote computer as if it was locally available

Design goals: A distributed system should make resources easily accessible; it should hide the fact that resources are distributed across a network; it should be open; and it should be scalable

![Screen Shot 2018-01-10 at 20.27.13](/Users/xiaoqi/Documents/ScreenShots/Screen Shot 2018-01-10 at 20.27.13.png)
​	

### 2018.1.11

Election Algorithm : Electing a coordinator

- The Bully Algorithm 

When any process notices that the coordinator is no longer responding to requests, it initiates an election. A process, P, holds an election as follows:

1. P sends an ELECTION message to all processes with higher numbers.

2. If no one responds, P wins the election and becomes coordinator.

3. If one of the higher-ups answers, it takes over. P's job is done.

  Eventually, all processes give up but one, and that one is the new coordinator. It announces its victory by sending all processes a message telling them that starting immediately it is the new coordinator.

- A Ring Algorithm

When any process notices that the coordinator is not functioning, it builds an ELECTION message containing its own process number and sends the message to' its successor

Eventually, the message gets back to the process that started it all. That process recognizes this event when it receives an incoming message containing its own process number. At that point, the message type is changed to COORDINATOR and circulated once again, this time to inform everyone else who the coordinator is (the list member with the highest number) and who the members of the new ring are. When this message has circulated once, it is removed and everyone goes back to work



### 2018.2.3

**System Architectures**

Centralized Architectures (36)

Decentralized Architectures

Hybrid Architectures



Threads (70)

Distributed Hash Table(188)

Synchronization (231)

Logical Clocks (244)

Mutual Exclusion (252)

Election Algorithm (263)

Consistency (273)

Agreement in Faulty Systems (331)

Replication in Peer-to-Peer File Systems (526)

Handling Byzantine Failures (529)

Peer-to-Peer Architectures (596)

### 2018.2.5



![DCIT](/Users/xiaoqi/Documents/ScreenShots/DCIT.jpeg)

1. **Points of Failure / Maintenance** : Centralized systems are easy to maintain as there is only a single point of failure. Decentralized have more but still finite. Distributed systems are the most difficult to maintain.
2. **Fault Tolerance / Stability** : Centralized can be highly unstable. Kill the leader and there will be chaos. Kill the leader for a decentralized system and you will have many decentralized systems. Distributed systems are very stable and a single failure doesn’t do much harm.
3. **Scalability / Max Population** : Centralized — low scalability, Decentralized — Moderate, Distributed — Infinite.
4. **Ease of development / Creation** : Centralized systems can be created really fast, you pick up a framework and apply it everywhere. For Decentralized and Distributed, you have to first work out the lower level details like resource sharing (trade) and communications (transport).
5. **Evolution / Diversity** : Since centralized systems follow a single framework, they don’t have diversity and evolve slowly. But for Decentralized and Distributed systems, once the basic infrastructure is in place, evolution is tremendous.

