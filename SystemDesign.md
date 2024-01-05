# System Design #
> ## Load Balancer ##
> ## Reverse Proxy ##
> ## CDN ##
> ## API Gateway ##
> ## Micro Services ##
> ## Message Queues ##
> ## Cache Servers ##
> ## Data Bases ##
-------------------------
> TCP vs UDP
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
