This is a simple implementation of a custom TCP protocol over UDP. 

## Usage
Run `make; ./tcp` on two terminals. One will act as the server and the other as the client.

## Implementation Details
* The custom TCP protocol is implemented over UDP. 

* The server sends packets in a sequential manner.

* The client receives the packets and sends an acknowledgement back to the server.

* If the server does not receive an acknowledgement within a fixed timeout interval, it retransmits the packet.

* The client receives the packets, reorders them, and prints the message.

* The socket is configured to be non-blocking. This allows the server to send packets without waiting for an acknowledgement.

* The various parameters of the protocol can be configured in `header.h`.

### Packet Format
The packet format is as follows:
```
+---------------------+---------------------+---------------------+---------------------+
|                              data                               |         seq         |
+---------------------------------------------------------------------------------------+
```
* `seq` is the sequence number of the packet. It is used to order the packets at the receiver's end. It is `SEQSIZE` bytes in size
* `data` is the payload of the packet. It contains the message to be sent. It takes up `DATASIZE` bytes in the packet.





### Q. How is your implementation of data sequencing and retransmission different from traditional TCP? 

There are some similarities to TCP.
- Although the underlying protocol is UDP, there is still some sort of reliability provided by the custom-TCP protocol implemented.
- A packet of the custom-TCP contains seq num just as in TCP protocol
- Retransmission after a fixed timeout interval. 


However, there are many differences in the way both of them work
- Packet metadata
    * TCP headers contain various control flags and fields for reliability, flow control, and sequencing. 
    * The custom-TCP implementation uses a simpler packet format with less overhead.

- Ordered delivery
    * TCP guarantees delivery of data packets in the same order, but the custom-TCP implementation does not guarantee the order of delivery. 
    * The packets may arrive out of order. It is the responsibility of the receiver to stitch them back together in order.

- Connection establishment
    * TCP establishes a connection between sender and receiver with a three-way handshake.
    * However, the custom-TCP implementation assumes to have established a connection via a connection request from the receiver.

- Flow control
    * Traditional TCP uses flow control mechanisms, such as TCP window scaling and congestion control, to manage the rate of data transmission based on network conditions. 
    * Custom implementation lacks these advanced flow control mechanisms.



### Q. How can you extend your implementation to account for flow control? 

- Sliding Window
    * Maintain a sliding window at the sender's side, limiting the number of unacknowledged packets that can be in transit simultaneously. 
    * Adjust the window size dynamically based on network conditions.

- Receiver Feedback
    * Modify the receiver to send acknowledgments not only for received packets but also to indicate its current buffer space 

- Sender Behaviour
    * The sender should adjust its transmission rate based on the receiver's feedback. It should send packets within the receiver's advertised window size. 
    * If the window size becomes zero, the sender should pause until the receiver advertises more space.
