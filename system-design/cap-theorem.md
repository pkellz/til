### CAP Theorem

**CAP theorem** is a statement / assertion in distributed systems that argues that two of ***C***onsistency, ***A***vailability, and ***P***artition tolerance are attainable for a given system.

*Consistency* - the quality of a system whereby reads always reflect the current state of the system - i.e. stale reads are unacceptable. For any write, all subsequent reads immediately & accurately reflect that write.

*Availability* - the quality of a system whereby service downtime & errors returned to the client are unacceptable.

If a system or part thereof becomes unavailable (e.g. node goes offline, resource is locked, node unreachable due to network partitions) the system can still recover and respond successfully.

Web server:
Assume a system of 3 nodes (web servers) sitting behind a load balancer:
<img width="650" height="323" alt="image" src="https://github.com/user-attachments/assets/262240a2-cea1-4411-8ea6-1d9ef294cb25" />

Node 2 goes offline due to electrical failures at the data center:
<img width="634" height="277" alt="image" src="https://github.com/user-attachments/assets/66587fb0-4710-49d6-a1e8-fc0a1f40f2b6" />

The load balancer guarantees availability by routing requests that would have gone to node 2 to other nodes. This might lead to increased latency if web traffic is being routed to servers closest to the user, but we avoid complete denial of service.

Database:

*Partition tolerance* - a "partition" in networking is a communication failure between two nodes (web / database servers). So a distributed system (a collection of independent computers working together as a cohesive unit) is partition tolerant when the system can still function where there is are networking failures such that two or more nodes cannot communicate with each other.

For any distributed system, partition tolerance is *expected*. That is, if you are designing a distributed system your system *should* and *will* be partition tolerant.

(TODO - explain more of WHY it's a given)

CAP theorem discusses / talks about the idea that you can only have 2 of the 3. And since partition tolerance is a given in distributed systems, the decision is often between prioritizing consistency vs. availability.

## Prioritizing Availability
If you prioritize availability, when a node goes down you must figure out a way to return a success to the user in spite of the failure.
Assume a system of 3 servers (nodes 1, 2, and 3) that sit behind a load balancer. 

Node 2 goes offline due to physical server issues in the data center.

Any requests that would be routed to Node 2 will now fail because the node is offline. Instead of returning an error, prioritizing availability might look like:
- have the load balancer route the request to another node
- store the request in the browser temporarily until the node is back online. User is none the wiser while browser keeps retrying the submission

Now that I think of it, availability strategies might look different depending on what your "node" is:
If the node is a web server...
- have the load balancer route the request to another node
If the node is in a database cluster...
- write to another node in the cluster (with the caveat that a subsequent READ from that user could result in stale data because of data differences between nodes)

Even in web server cases where nodes don't necessarily "go down", if you have a lot of heavy processing work that could & should happen asynchronously you could adopt an 'eventually consistent' model such that expensive work is queued for further processing, however you immediately send a success to the client acknowledging receipt of the work (not necessarily that it was completed successfully, though).

## Prioritizing Consistency
To prioritize consistency means you will do whatever you can to maintain the integrity of the data, even if that means denying service. You prioritize data correctness over availability.
- If a node goes down, you may fail the request and return an error to the user and ask them the try again.

Systems where consistency should take precedence are systems where stale data is intolerable - e.g. banking systems, high-frequency trading. Social media platforms on the other hand can stand some eventual consistency. What users will not stand though is low availability (platform won't load). So in that case you'd prioritize availability.

In terms of ACID-ic database systems, prioritizing consistency might mean locking certain records while transactions are taking place such that other things cannot affect it.

Generally, as a default prioritize availability over consistency unless dealing in a safety-critical, accuracy-critical domain where stale data is intolerable.
