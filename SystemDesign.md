# System Design #
- [Load Balancer](#load-balancer)
- [Reverse Proxy](#reverse-proxy)
- [CDN](#cdn)
- [API Gateway](#api-gateway)
- [Microservices](#microservices)
- [Message Queue](#message-queue)
- [Caching](#caching)
    - [Cache update strategies](#cache-update-strategies)
      - [Cache Aside](#cache-aside)
      - [Write Through](#write-through)
      - [Write Behind](#write-behind)
- [Databases](#databases)
- [NoSql](#no-sql)
    - [Key Value Store](#key-value-store)
    - [Document Store](#document-store)
    - [Wide Column Store](#wide-column-store)
    - [Graph Store](#graph-store)
- [Security](#security)
- [Durability](#durability)
- [Replication](#replication)
    - [Types of Replication](#replication-types)
    - [Replication Strategies](#replication-strategies)
- [Sharding](#sharding)
    - [Vertical Sharding](#vertical-sharding)
    - [Horizontal Sharding](#horizontal-sharding)
- [TCP vs UDP](#tcp-vs-udp)
    - [TCP](#tcp)
    - [UDP](#udp)
- [General Latency Numbers](#latency-numbers)
- [Availability Patterns](#availability-patterns)
- [Availability Numbers](#availability-numbers)
- [Capacity Estimations](#capacity-estimations)
- [Cassandra](#cassandra)
    - [Gossip](#gossip)
    - [Partitioner](#partitioner)
    - [Replication Factor](#replication-factor)
    - [Replication strategy](#replication-strategy)
    - [Snitch](#snitch)
- [Kafka](#kafka)
    - [Brokers](#brokers)
    - [Topics/Queues](#topics-or-queues)
    - [Replication](#replication)
- [Hashing](#hashing)
- [Consistent Hashing](#consistent-hashing)
- [References](#references)

<a id="load-balancer"></a>
## Load Balancer ##
````
Load balancers are effective at:
  - Preventing requests from going to unhealthy servers
  - Preventing overloading resources
  - Helping to eliminate a single point of failure

Layer 4 load balancers : Layer 4 load balancers look at info at the transport layer to decide how to distribute requests. Generally, this involves the source, destination IP addresses, and ports in the header, but not the contents of the packet.

Layer 7 load balancers : Layer 7 load balancers look at the application layer to decide how to distribute requests. This can involve contents of the header, message, and cookies. Layer 7 load balancers terminate network traffic, reads the message, makes a load-balancing decision, then opens a connection to the selected server.

At the cost of flexibility, layer 4 load balancing requires less time and computing resources than Layer 7, although the performance impact can be minimal on modern commodity hardware.
````
<a id="reverse-proxy"></a>
## Reverse Proxy ##
<a id="cdn"></a>
## CDN ##
````
- Push CDN : Content is pushed by the server to CDN whenever new content appears on server. Server decides how often content should be refreshed on CDN. Works well with low traffic sites as there is no uneccessary pull by CDN.
- Pull CDN : Content is pulled by the CDN whenever a new request comes and it is cached with a defined TTL. Once TTL expires the content is refreshed. Works well with high traffic sites. Helps in distributing the load from the servers
````
<a id="api-gateway"></a>
## API Gateway ##
<a id="microservices"></a>
## Micro Services ##
<a id="message-queue"></a>
## Message Queues ##
````
Message queues receive, hold, and deliver messages. If an operation is too slow to perform inline, you can use a message queue
- If queues start to grow significantly, the queue size can become larger than memory, resulting in cache misses, disk reads, and even slower performance
- Backpressure can help avoiding slowness by limiting the size of queue.
- Client gets 503 when queue is full
````
<a id="caching"></a>
## Caching ##
````
Caching help reduce load on servers. Caches can be located on the client side (OS or browser), server side, or in a distinct cache layer.
- CDN caching
- Caching at Reverse Proxies
- Application Layer Caching : Inmemory caches such as Redis, Memcache
- Database caching : Default caching on database servers
````
<a id="cache-update-strategies"></a>
   - ### Cache update strategies ###
     <a id="cache-aside"></a>
     - Cache-aside
       ````
       On cache miss the data is fetched from the database and updated into the cache server.
       - Multiple network trips required on cache miss. So some reads can be very slow.
       - Data can become stale if cache TTL is not defined properly
       - When a node fails, it is replaced by a new, empty node, which leads to more cache misses hence increased latency
       ````
       <a id="write-through"></a>
     - Write Through
       ````
       Application uses the cache as the main data store, reading and writing data to it, while the cache is responsible for reading and writing to the database.
       - Slow writes as data has to be replicated into the database servers synchronously.
       - Most data written recently may never be read. Hence it has to be used with cache-aside strategy
       - addition of new servers causes much more cache misses.
       ````
     <a id="write-behind"></a>
     - Write Behind
       ````
       Similar to write through but data is not writted to DB server synchronously instead it is pushed to a queue to be consumed by DB servers
       - Better write latency
       - Can impact durability of written data as data cached may never be written to DB servers
       ````
<a id="databases"></a>
## Databases ##
<a id="federation"></a>
   - ### Federation ###
     ````
     - Federation (or functional partitioning) splits up databases by function.
       For example, instead of a single, monolithic database, you could have three databases: forums, users, and products,
       resulting in less read and write traffic to each database and therefore less replication lag.
     - Smaller databases result in more data that can fit in memory, which in turn results in more cache hits due to improved cache locality
     - Joining data from two databases is more complex with a server link.
     - Federation adds more hardware and additional complexity.
     ````
     <a id="sharding"></a>
   - ### Sharding ###
     ````
     - Post federation individual product databases can be divided into smaller subsets of databases called Shards to further distribute the load.
     - It distributes data across different databases such that each database can only manage a subset of the data. Taking a users database as an example,
       as the number of users increases, more shards are added to the cluster.
     - Less read and write traffic
     - Less replication lag
     - More cache hits
     - Smaller indexes so more data in memory
     - Common ways to shard a table of users is either through the user's last name initial or the user's geographic location.
     ````
     <a id="denormalization"></a>
   - ### Denormalization ###
     ````
     - After federation and sharding some queries might lead to complex joins across multiple tables. This can impact performance to queries.
       Denormalization help in alleviating some of those joins by keeping redundant data in some of the tables.
     - Materialize views can be used to keep redundant data consistent.
     - In most systems, reads can heavily outnumber writes 100:1 or even 1000:1. A read resulting in a complex database join can be very expensive,
       spending a significant amount of time on disk operations.
     ````
     <a id="sql-tuning"></a>
   - ### SQL Tuning ###
     <a id="indexes"></a>
   - ### Indexes ###
<a id="no-sql"></a>
## NoSQL ##
````
NoSQL is a collection of data items represented in a key-value store, document store, wide column store, or a graph database. Data is denormalized, and joins are generally done in the application code. Most NoSQL stores lack true ACID transactions and favor eventual consistency.

NoSQL makes it easier to scale out since the data related to a specific entity is stored in one document instead of multiple tables over nodes

BASE is often used to describe the properties of NoSQL databases. In comparison with the CAP Theorem, BASE chooses availability over consistency.

Basically available - the system guarantees availability.
Soft state - the state of the system may change over time, even without input.
Eventual consistency - the system will become consistent over a period of time, given that the system doesn't receive input during that period.

Uses Bloom Filter to quickly find out if a SSTable contains a key or not
````
<a id="key-value-store"></a>
  - ### Key Value Store ###
    ````
    - Datastructure similar to HashMaps
    - Fast O(1) reads and writes.
    - maintains keys in lexicographic order(Dictionary Order)
    - high performance data store with simple structure. Generally used in distributed caches.
    - Example - Redis and Memcache
    ````
    <a id="document-store"></a>
  - ### Document Store ###
    ````
    - Datastructure like Hashmap with values as document
    - Can store complex, semi-structured documents like XML, json, binary.
    - provide APIs or a query language to query based on the internal structure of the document itself
    - Example - MongoDB, CouchDB, Elasticsearch
    ````
    <a id="wide-column-store"></a>
  - ### Wide Column Store ###
    ````
    - Datastructure like Map of Map (ColumnFamily<RowKey, Columns<ColKey, Value, Timestamp>>)
    - A wide column store's basic unit of data is a column (name/value pair).
    - columns a grouped together to form column families or super column families
    - can access each column independently with a row key, and columns with the same row key form a row
    - Each row value contains a timestamp for versioning and for conflict resolution
    - offer high availability and high scalability
    - Example : BigTable, HBase, Cassandra
    ````
    <a id="graph-store"></a>
  - ### Graph Store ###
    ````
    - Set of nodes representing complex many-to-many relationship between entities
    - each node is a record and each arc is a relationship between two nodes
    - offer high performance for data models with complex relationships such as in social network
    - Example - Neo4j, FlockDB
    ````
<a id="security"></a>
## Security ##
````
- Open up only necessary ports. Allow the web server to respond to incoming requests from:
    - 80 for HTTP
    - 443 for HTTPS
    - 22 for SSH to only whitelisted IPs
    - Prevent the web server from initiating outbound connections
- Encrypt data in transit and at rest
- Sanitize all user inputs or any input parameters exposed to user to prevent XSS and SQL injection.
- Use parameterized queries to prevent SQL injection.
- Use the principle of least privilege.
````
<a id="durability"></a>
## Durability ##
- Commit logs
- Data redundancy/replication (Multi-DC, Multi-AZ, Multi-Region)

<a id="replication"></a>
## Replication ##
````
Replication refers to keeping multiple copies of the data at various nodes (preferably geographically distributed) to achieve availability, scalability, and performance
````
<a id="replication-types"></a>
  - ### Types of Replication ###
    <a id="sync-replication"></a>
    - #### Synchronous Replication ####
    ````
    Primary node will wait to return the response till all secondary nodes have received the data.
    So all nodes are always updated. This will cause high latency and low fault tolerance.
    As even one node is slow or fails during request execution it will impact the SLA of whole request.
    ````
    <a id="async-replication"></a>
    - #### Asynchronous Replication ####
    ````
    Primary node can return the response one the changes is applied locally and propagate the
    change to secondary node in async. This will have low latency and better fault tolerance. This can cause data consistency
    issues as all the nodes may not get updated in time or at all.
    ````
    <a id="replication-strategies"></a>
  - ### Replication Stategies ###
    - #### Single leader or primary-secondary replication ####
      ````
      - Primary handles all the writes and secondary nodes are responsible for read.
      - Provides efficient read scalability
      - Most appropriate for read heavy applications
      - This may lead to write bottlenecks and failures if primary fails. Can be handled by having a primary in standby or promoting a read replica as primary.
      - Replication of data can be done as statement based where each query statement is sent to replicas to be executed(functions like sysdate() or now() can cause problems)
      or WAL based where commit log is shared with replicas.
      - Asyc replication of data between primary and secondary may lead to inconsistent data.
      ````
    - #### Multi-leader replication ####
      ````
      - There are multiple leaders who handle writes and all writes are shared with all primary and all secondary
      - This strategy can lead to conflict in the DB state if two separate leaders accept write for same data and when these changes
      arrive at the other leader as part of replication it can lead to conflict.
      - Steps to handle conflict :
          - Conflicts can be avoided by making sure writes for a particular record always goes to same leader.
          - Conflicts can be resolved by applying Last Write Wins strategy.
      ````
    - #### Peer-to-peer or leaderless replication ####
      ````
      - All replicas have same weightage and all can handle writes.
      - The issues replicated to replication lag and data inconsistency in previous strategies is handles here using quorums.
      - In read request the request is considered successfull if certains number of replicas(r) are able to serve the same read data.
      This makes sure that even if certain number of read replicas are not available the system is able to serve reads. And data consistency is resolved
      by r replicas aggreeing on a particular value.
      - In write requests if the certain number of replicas(w) are able to accept the writes then it is considered succesful. Agains conflicts are resolved by agreeing
      on a single value.
      ````
<a id="sharding"></a>
## Sharding ##
````
Dividing data into smaller chunks in a balanced manner so that data is divided equally among separate nodes and traffic is also evenly distributed.
````
   <a id="vertical-sharding"></a>
   - ### Vertical Sharding ###
     ````
     ````
     <a id="horizontal-sharding"></a>
   - ### Horizontal Sharding ###
     ````
     ````

<a id="tcs-vs-udp"></a>
## TCP vs UDP ##

  <a id="tcs"></a>
  - ### TCP ###
    ````
    TCP is a connection-oriented protocol over an IP network. Connection is established and terminated using a handshake. All packets sent are guaranteed to reach the destination in the original order and without corruption through:
    
    Sequence numbers and checksum fields for each packet
    Acknowledgement packets and automatic retransmission
    
    TCP also implements flow control and congestion control by bufferring of messages in an internal queue.
    
    Use TCP over UDP when:
     - You need all of the data to arrive intact
     - You want to automatically make a best estimate use of the network throughput
    ````
    <a id="udp"></a>
  - ### UDP ###
    ````
    UDP is connectionless. Datagrams (analogous to packets) are guaranteed only at the datagram level. Datagrams might reach their destination out of order or not at all. UDP does not support congestion control. Without the guarantees that TCP support, UDP is generally more efficient.
    
    Use UDP over TCP when:
     - You need the lowest latency
     - Late data is worse than loss of data
     - You want to implement your own error correction
    ````
<a id="latency-numbers"></a>
## General Latency Numbers ##
````
L1 cache reference ..................  0.000011 seconds (SR-71 travels 1cm)
Branch mispredict ...................  0.000110 sec (Bullet travels 4cm)
L2 cache reference ..................  0.000154 sec (Boeing 777 travels 4cm)
Mutex lock/unlock ...................  0.00055 sec (Time before you hear a fingersnap made in front of your face [speed of sound across 19cm])
Main memory reference ...............  0.0022 sec (Camera shutter on a sunny day [1/400 - 1/500 shutter speed])
Compress 1K bytes with Zippy ........  0.066 sec (Lightning bolt travels 4km from cloud to ground)
Send 2K bytes over 1 Gbps network ...  0.44 sec (Fastball from pitcher to home plate)
SSD random read .....................  3.3 sec (SR-71 travels 3.1km)
Read 1 MB sequentially from memory ..  5.5 sec (Yawn)
Round trip within same datacenter ... 11.0 sec (A Cheetah runs 200m)
Read 1 MB sequentially from SSD* .... 22.0 sec (Usain Bolt runs 200m)
Disk seek ...........................  3.6 minutes (Brewing coffee in a French Press)
Read 1 MB sequentially from disk ....  7.3 min (A performance of the first movement of Beethoven's 5th Symphony)
Send packet CA->Netherlands->CA ..... 55.0 min (Going for a brisk 5km walk)
````
<a id="availability-patterns"></a>
## Availability patterns ##
````
 1) Fail Over
    - Active Passive (Master Slave)
    - Active Active (Master Master)
 2) Replication
    - Read-Write replicas
    - Write-Write-Read-Read replicas (Can cause conflicting writes coming in to the system)
 3) Load Balancing (To avoid overloading a single service)
    - Random
    - Round robin
    - Least busy
    - Sticky session / cookies
    - By request parameters
 4) Multi-AZ and Multi Region clustering 
````
<a id="availability-numbers"></a>
### Availability Numbers ###
````
99.9% availability - three 9s**
-------------------------------
Duration	Acceptable downtime
Downtime per year	8h 45min 57s
Downtime per month	43m 49.7s
Downtime per week	10m 4.8s
Downtime per day	1m 26.4s

**99.99% availability - four 9s**
--------------------------------
Duration	Acceptable downtime
Downtime per year	52min 35.7s
Downtime per month	4m 23s
Downtime per week	1m 5s
Downtime per day	8.6s
````
<a id="capacity-estimations"></a>
## Capacity Estimations ##
````
    - 1 million request/day = 12 request/second
    - 1 million bytes = 1MB
    - Seconds in a Day = 86400
    - Seconds in a year = 31M
````
<a id="cassandra"></a>
## Cassandra ##
````
Cassandra is designed to handle big data workloads across multiple nodes with no single point of failure. 
Its architecture is based on the understanding that system and hardware failures can and do occur. Cassandra addresses the problem of failures by employing
a peer-to-peer distributed system across homogeneous nodes where data is distributed among all nodes in the cluster. Each node frequently exchanges state
information about itself and other nodes across the cluster using peer-to-peer gossip communication protocol.
- maintains a sequentially written commit logs to ensure data durability
- Data is then indexed and written to an in-memory structure, called a memtable, which resembles a write-back cache.
- Everytime the memtable is full the data is flushed to SSTable in disk.
- All writes are automatically partitioned and replicated throughout the cluster.
- SSTables are consolidated by periodically running compaction process where discarded data is deleted.
- Any node can act as request coordinator in the cluster.
- Once coordinator is assigned it then decides which node in the cluster should serve the read/write requests.
````
<a id="gossip"></a>
  - ### Gossip ###
    ````
    A peer-to-peer communication protocol to discover and share location and state information about the other nodes in a Cassandra cluster.
    Gossip information is also persisted locally by each node to use immediately when a node restarts.
    ````
    <a id="practitioner"></a>
  - ### Partitioner ###
    ````
    A partitioner is a hash function that derives a token from the primary key of a row and uses the token to locate the node that will receive
    the first replica of the data. A rowkey can be used as the partition key as well.
    ````
    <a id="replication-factor"></a>
  - ### Replication Factor ###
    ````
    It determines how many replicas of a row will be maintained.
    - A replication factor 1 means there is only one replica of a row on 1 node.
    - A replication factor of 2 means there will be 2 replicas of the row and each of those replicas will be on a different node in the cluster.
    ````
    <a id="replication-strategy"></a>
  - ### Replication strategy ###
    ````
    Number of write replicas + Number of read replicas > Replication factor
    ````
    <a id="snitch"></a>
  - ### Snitch ###
    ````
    A snitch determines which datacenters and racks nodes belong to. They inform Cassandra about the network topology so that requests are routed
    efficiently and allows Cassandra to distribute replicas by grouping machines into datacenters and racks
    ````
<a id="kafka"></a>
## Kafka ##
````
Kafka is a persistent, distributed, replicated pub/sub messaging system. Producers send message to a group of brokers who store then in an append only commit log.
Then consumers read from those brokers based on offset.
  - Uses NIO Filechannel for efficient communications.
  - Prioritises throughput over consistency.
  - Producers determine the partition where the message should be sent based on routing key.
  - Kafka only gaurantees ordering of messages per partition. If messages are on different partiion then they may not be ordered.
````
<a id="brokers"></a>
  - ### Brokers ###
    ````
    - Receive messages from Producers (push),deliver messages to Consumers (pull)
    - Responsible for persisting the messages for some time
    - Relatively lightweight - mostly just handling TCP connections and keeping open file handles to the queue files
    ````
    <a id="topics-or-queues"></a>
  - ### Topics/Queues ###
    ````
    - Append only log files for fast sequential write and sequential read.
    - Patitioned across multiple brokers
    ````
    <a id="replication"></a>
  - ### Replication ###
    ````
    - All topic partitions are replicated with one replica acting as master.
    - All read and writes must go to master only.
    - Replicas are just for Fault Tolerance
    ````
<a id="hashing"></a>
## Hashing ##
````
Hashing is the process of transforming any given key or a string of characters into another value. This is usually represented by a shorter,
fixed-length value or key that represents and makes it easier to find or employ the original string.

Popular Hashing Functions :
  - MD5, SHA-1 - Commonly used, used to be secure, but no longer collision resistant
  - SHA-2 - Commonly used, secure. It's a family of functions with different output size.
````
<a id="consistent-hashing"></a>
## Consistent Hashing ##
````
- Nodes are arranged in a logical ring structure.
- For each node the nodeId is passed through a hash function and based on the hash value the node is placed on the ring.
- When we receive a request, we take the key and pass it through same hash function and based on the value we map it on a place in ring.
- The value is stored on the clockwise neighbour of the key location on the ring.
- Sometime some nodes can become bottleneck due to uneven distribution of requests on the ring and many keys mapping to same neighbourhood on the ring.
- To distribute the load evenly we can add virtual node. To achieve that we can pass the nodeId to multiple has functions and place the result value
on multiple places on the ring. Based on hardware capacity some node can have more representation on the ring as they could handle more requests.
````
<a id="references"></a>
## References ##
- https://tianpan.co/notes/2016-02-13-crack-the-system-design-interview/
- https://github.com/donnemartin/system-design-primer

