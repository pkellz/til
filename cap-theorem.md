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
- have the load balancer route the request to another node
If the node is in a database cluster...
- write to another node in the cluster (with the caveat that a subsequent READ from that user could result in stale data because of data differences between nodes)

Even in web server cases where nodes don't necessarily "go down", if you have a lot of heavy processing work that could & should happen asynchronously you could adopt an 'eventually consistent' model such that expensive work is queued for further processing, however you immediately send a success to the client acknowledging receipt of the work (not necessarily that it was completed successfully, though).

## Prioritizing Consistency
To prioritize consistency means you will do whatever you can to maintain the integrity of the data, even if that means denying service. You prioritize data correctness over availability.
- If a node goes down, you may fail the request and return an error to the user and ask them the try again.

Systems where consistency should take precedence are systems where stale data is intolerable - e.g. banking systems, high-frequency trading. Social media platforms on the other hand can stand some eventual consistency. What users will not stand though is low availability (platform won't load). So in that case you'd prioritize availability.

Generally, as a default prioritize availability over consistency unless dealing in a safety-critical, accuracy-critical domain where stale data is intolerable.
