# System Design #
## Load Balancer ##
## Reverse Proxy ##
## CDN ##
## API Gateway ##
## Micro Services ##
## Message Queues ##
## Cache Servers ##
## Data Bases ##
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
**General Latency Numbers**
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
**Availability patterns**
````
 1) Fail Over
    - Active Passive (Master Slave)
    - Active Active (Master Master)
 2) Replication
    - Read-Write replicas
    - Write-Write-Read-Read replicas (Can cause conflicting writes coming in to the system)
 3) Load Balancing (To avoid overloading a single service)
    - Routing based on health of replicas
    - Round robin
    - Hash key based routing
 4) Multi-AZ and Multi Region clustering 
````
   
