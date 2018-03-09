### Introduction

Distributed System Definition: 

> A distributed system is a collection of autonomous computing elements that appears to its users as a single coherent system

**Types of DS**

- Distributed Computing Systems (computation load across machines)
- Distributed Information Systems (provide info from heterogeneous information sources)
- Distributed Pervasive/Embedded Systems (ad-hoc interconnection of mobile devices)

**Design of DS (Transparency Properties)**

- Access : hiding defferences in data representation and how a resource is accessed
- location : hiding where a resource is located
- migration : hiding a resource might move to another location 
- relocation : hiding that a resource may be moved while in use
- replication: hiding that a resource is replicated
- concurrency : hiding that a resorce may be used concurrently by multiple users
- performance : allowing the system to be reconfigured to improve performance 
- scaling : expanding in scale without change system structure or algorithm
- failure : hiding failures and recovery of a rescouce -> reliability 

"Heterogeneity, Openness, Interoperability, Marshalling"

**Scalability**: 

- Size : the ability of adding additional users and rescouces to the system
- Geographical : users and rescources might lie far apart
- Administrative : a system can be eaisily managed even if it spans multiple administrative domains

**Typical Pitfalls**

- Network is reliable, secure & homogeneous
- Topology does not change
- Latency is zero
- Bandwidth is infinite
- Transport cost is zero
- There is only a single administrator

**DS Transactions**

- Atomicity: a transaction is all or nothing
- Consistency: transform data from one valid state to another
- Isolation: transactions must not influence each other 
- Durability: a committed transaction is persistent 



### Architecture

SDU: Service Data Unit, Layer n-1 treats layer n's data as SDU

PCI: Protocol Control Information

PDU: Protocol Data Unit, originate and terminate at the same layer

**Architectural Styles**

- Object-based architecture: communicate with objects; very flexible; objects can be distribued across multiple machines; objects are sent as messages
- Event-based architecture: Propagation of events; publish/subscribe; event bus can forward events accross machines; shared data spaces
- Client-Server architecture: Presentation, Business logic, Data
- Decentralized architecture: server-coordinated; super-peer; distributed hash table 

Self-managing DS: Goal(resilience, robustness, scalability), Self-(configuration, managing, healing, optimizing, -*)



### Peer to Peer

Napster:

- advantages: simple (all files can be found in a single step without a central index)
- disadvantages: central server is a single point of failure and makes the system vulnerable; computational power, memory of central server needs to grow with the number of users—no inherent scalability; not really p2p system

Gnutella: (first fully decentralized system)

- no central control mechanisms
- Bootstrapping: try to connect a node already in the overlay
- The network is explored by a ping message. A receiving node forwards the ping to all its neighbors (flooding). Node answer to a ping by a pong message. 
- Advantages: Fully decentralized; extremely robust
- Disadvantages: long response time due to the excessive flooding; flooding is inefficient; limited by flooding scope, search are performed in part of the network

Kademlia: (structured P2P system; User XOR distance metric and prefix routing)



### Processes

RPC semantics

Ideal semantics: exactly-once (remote invocation will happen exactly a single time)

Realizable semantics: 

- At-least-once: Multiple invocations possible
- At-most-once: invocation may not be done repeatedly 

Idempotent requests: multiple executions of the same reqeust lead to the same result; many tasks are inherently idempotent: read-only, query database, delete a file

Inherently non-idempotent tasks: transferring money, placing an order

TCP-switch: (Performing load balancing) On incoming request the TCP-switch determines a suitable server and forwards the request to the server. Server answers the client directly with spoofing IP address of the switch. 



### Synchronization

UTC: Universal Coordinated Time

Clock Skew: in practice $1-p < dC/dt < 1+p $

Algorithm by Cristian

Berkeley Algorithm: Active instead of passive time server

NTP: Network Time Protocol

Causality: cause always happens before the effect (causally depend ording relationship $<_c$)

Logical Clocks 

Lamport Clock/Extended Lamport Clock

Vector Clock

Mutual Exclusion: Goals (safety, deadlock free, fairness, liveness)

Majority vote: 

Lamport's Distributed Mutual Exclusion: All nodes maintain a priority queue ordered according to time stamps (lowest element in the head of queue)

Ricart & Agrawala: Optimization of Lamport's Distributed Mutual Exclusion

Raymond's Mutual Exclusion

**Coordinator and Election**

- Bully Algorithm: Each node know IDs of all other nodes
- Ring Elecition Algorithm: Nodes are logically ordered on a ring; topology is known to each node in the system
- Chang and Roberts: election messages are just forwarded if they can win the election; highest priority wins





### 2018-1-27

**Solution 1 for context switch**

The main distinction between a thread switch and a process switch is that during a thread switch, the virtual memory space remains the same, while it does not during a process switch. Both types involve handing control over to the operating system kernel to perform the context switch. The process of switching in and out of the OS kernel along with the cost of switching out the registers is the largest fixed cost of performing a context switch.

A more fuzzy cost is that a context switch messes with the processors cacheing mechanisms. Basically, when you context switch, all of the memory addresses that the processor "remembers" in it's cache effectively become useless. The one big distinction here is that when you change virtual memory spaces, the processor's Translation Lookaside Buffer (TLB) or equivalent gets flushed making memory accesses much more expensive for a while. This does not happen during a thread switch.

**Solution 2 for context switch**

Process context switching involves switching the memory address space. This includes memory addresses, mappings, page tables, and kernel resources—a relatively expensive operation. On some architectures, it even means flushing various processor caches that aren't sharable across address spaces. For example, x86 has to flush the TLB and some ARM processors have to flush the entirety of the L1 cache!

Thread switching is context switching from one thread to another in the same process (switching from thread to thread across processes is just process switching).Switching processor state (such as the program counter and register contents) is generally very efficient.