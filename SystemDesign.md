# System Design #
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
## Reverse Proxy ##
## CDN ##
````
- Push CDN : Content is pushed by the server to CDN whenever new content appears on server. Server decides how often content should be refreshed on CDN. Works well with low traffic sites as there is no uneccessary pull by CDN.
- Pull CDN : Content is pulled by the CDN whenever a new request comes and it is cached with a defined TTL. Once TTL expires the content is refreshed. Works well with high traffic sites. Helps in distributing the load from the servers
````
## API Gateway ##
## Micro Services ##
## Message Queues ##
````
Message queues receive, hold, and deliver messages. If an operation is too slow to perform inline, you can use a message queue
- If queues start to grow significantly, the queue size can become larger than memory, resulting in cache misses, disk reads, and even slower performance
- Backpressure can help avoiding slowness by limiting the size of queue.
- Client gets 503 when queue is full
````
## Caching ##
````
Caching help reduce load on servers. Caches can be located on the client side (OS or browser), server side, or in a distinct cache layer.
- CDN caching
- Caching at Reverse Proxies
- Application Layer Caching : Inmemory caches such as Redis, Memcache
- Database caching : Default caching on database servers
````
   - ### Cache update strategies ###
     - Cache-aside
       ````
       On cache miss the data is fetched from the database and updated into the cache server.
       - Multiple network trips required on cache miss. So some reads can be very slow.
       - Data can become stale if cache TTL is not defined properly
       - When a node fails, it is replaced by a new, empty node, which leads to more cache misses hence increased latency
       ````
     - Write Through
       ````
       Application uses the cache as the main data store, reading and writing data to it, while the cache is responsible for reading and writing to the database.
       - Slow writes as data has to be replicated into the database servers synchronously.
       - Most data written recently may never be read. Hence it has to be used with cache-aside strategy
       - addition of new servers causes much more cache misses.
       ````
     - Write Behind
       ````
       Similar to write through but data is not writted to DB server synchronously instead it is pushed to a queue to be consumed by DB servers
       - Better write latency
       - Can impact durability of written data as data cached may never be written to DB servers
       ````
## Databases ##
   - ### Federation ###
     ````
     - Federation (or functional partitioning) splits up databases by function.
       For example, instead of a single, monolithic database, you could have three databases: forums, users, and products,
       resulting in less read and write traffic to each database and therefore less replication lag.
     - Smaller databases result in more data that can fit in memory, which in turn results in more cache hits due to improved cache locality
     - Joining data from two databases is more complex with a server link.
     - Federation adds more hardware and additional complexity.
     ````
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
   - ### Denormalization ###
     ````
     - After federation and sharding some queries might lead to complex joins across multiple tables. This can impact performance to queries.
       Denormalization help in alleviating some of those joins by keeping redundant data in some of the tables.
     - Materialize views can be used to keep redundant data consistent.
     - In most systems, reads can heavily outnumber writes 100:1 or even 1000:1. A read resulting in a complex database join can be very expensive,
       spending a significant amount of time on disk operations.
     ````
   - ### SQL Tuning ###
   - ### Indexes ###
## NoSQL ##
````
NoSQL is a collection of data items represented in a key-value store, document store, wide column store, or a graph database. Data is denormalized, and joins are generally done in the application code. Most NoSQL stores lack true ACID transactions and favor eventual consistency.

BASE is often used to describe the properties of NoSQL databases. In comparison with the CAP Theorem, BASE chooses availability over consistency.

Basically available - the system guarantees availability.
Soft state - the state of the system may change over time, even without input.
Eventual consistency - the system will become consistent over a period of time, given that the system doesn't receive input during that period.
````
  - ### Key Value Store ###
    ````
    - Datastructure similar to HashMaps
    - Fast O(1) reads and writes.
    - maintains keys in lexicographic order(Dictionary Order)
    - high performance data store with simple structure. Generally used in distributed caches.
    - Example - Redis and Memcache
    ````
  - ### Document Store ###
    ````
    - Datastructure like Hashmap with values as document
    - Can store complex, semi-structured documents like XML, json, binary.
    - provide APIs or a query language to query based on the internal structure of the document itself
    - Example - MongoDB, CouchDB, Elasticsearch
    ````
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
  - ### Graph Store ###
    ````
    - Set of nodes representing complex many-to-many relationship between entities
    - each node is a record and each arc is a relationship between two nodes
    - offer high performance for data models with complex relationships such as in social network
    - Example - Neo4j, FlockDB
    ````
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
## Durability ##
- Commit logs
- Data replication
-------------------------
## TCP vs UDP ##

**TCP**
````
TCP is a connection-oriented protocol over an IP network. Connection is established and terminated using a handshake. All packets sent are guaranteed to reach the destination in the original order and without corruption through:

Sequence numbers and checksum fields for each packet
Acknowledgement packets and automatic retransmission

TCP also implements flow control and congestion control by bufferring of messages in an internal queue.

Use TCP over UDP when:
 - You need all of the data to arrive intact
 - You want to automatically make a best estimate use of the network throughput
````
**UDP**
````
UDP is connectionless. Datagrams (analogous to packets) are guaranteed only at the datagram level. Datagrams might reach their destination out of order or not at all. UDP does not support congestion control. Without the guarantees that TCP support, UDP is generally more efficient.

Use UDP over TCP when:
 - You need the lowest latency
 - Late data is worse than loss of data
 - You want to implement your own error correction
````
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
  - ### Gossip ###
    ````
    A peer-to-peer communication protocol to discover and share location and state information about the other nodes in a Cassandra cluster.
    Gossip information is also persisted locally by each node to use immediately when a node restarts.
    ````
  - ### Partitioner ###
    ````
    A partitioner is a hash function that derives a token from the primary key of a row and uses the token to locate the node that will receive
    the first replica of the data. A rowkey can be used as the partition key as well.
    ````
  - ### Replication Factor ###
    ````
    It determines how many replicas of a row will be maintained.
    - A replication factor 1 means there is only one replica of a row on 1 node.
    - A replication factor of 2 means there will be 2 replicas of the row and each of those replicas will be on a different node in the cluster.
    ````
  - ### Replication strategy ###
    ````
    Number of write replicas + Number of read replicas > Replication factor
    ````
  - ### Snitch ###
    ````
    A snitch determines which datacenters and racks nodes belong to. They inform Cassandra about the network topology so that requests are routed
    efficiently and allows Cassandra to distribute replicas by grouping machines into datacenters and racks
    ````
## Kafka ##
````
Kafka is a persistent, distributed, replicated pub/sub messaging system. Producers send message to a group of brokers who store then in an append only commit log.
Then consumers read from those brokers based on offset.
  - Uses NIO Filechannel for efficient communications.
  - Prioritises throughput over consistency.
  - Producers determine the partition where the message should be sent based on routing key.
  - Kafka only gaurantees ordering of messages per partition. If messages are on different partiion then they may not be ordered.
````
  - ### Brokers ###
    ````
    - Receive messages from Producers (push),deliver messages to Consumers (pull)
    - Responsible for persisting the messages for some time
    - Relatively lightweight - mostly just handling TCP connections and keeping open file handles to the queue files
    ````
  - ### Topics/Queues ###
    ````
    - Append only log files for fast sequential write and sequential read.
    - Patitioned across multiple brokers
    ````
  - ### Replication ###
    ````
    - All topic partitions are replicated with one replica acting as master.
    - All read and writes must go to master only.
    - Replicas are just for Fault Tolerance
    ````

