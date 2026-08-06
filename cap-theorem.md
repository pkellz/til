### CAP Theorem

CAP theorem is the proposition that declares that for a distributed system two of consistency, availability, and partition tolerance is achievable.

Consistency - the quality describing data that says that all data that is read is always current. There are no stale reads. Every write takes immediate effect for any subsequent reads.
Consistency is the quality that all writes are immediately reflected in the system.

Availability - the system does not ever deny service. If one node in the system goes down another node can receive the request instead. 

Partition tolerance - a "partition" in networking-speak is a communication failure between two nodes. So a distributed system (any system in which resources are spread across several nodes / machines),
is partition tolerance if when the system can still operate while two are more nodes cannot communicate with each other for a period of time. For any distributed system, partition tolerance is
sort of a "given". A production distributed system is **expected** to be partition tolerant.

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
If the node is a database server...
If the node is a cache...
If the node is file store...
