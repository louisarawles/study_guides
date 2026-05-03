**Networking**

How to write abstraction layers to avoid continuously reasoning about code all the time

_Enable machine-to-machine communication_
Goal: enable applications that span multiple computers
Challenge: realities of the real-world

_Sockets_
* base primitive; 
* Starting abstraction: sending individual messages (packets) to appear like information just flowing
* open _connection_ then ...
  * read + write just like a terminal file

Mailbox model
* _mailbox_ abstraction: send/receive messages
* real internet: mailbox-style communication
  * send "letters" (packets) to particular mailboxes
  * have "envelope" (header)

Networking Layers
* key elements:
  * application
  * transport  
    * reliability
    * segments/datagrams
  * network
    * packets
  * link
    * frames
  * physical
    * ie, communicating 1s and 0s to one another

Layer encapsulation
* :: upper layers implemented using lower layers
* ie:
  * to make unreliable network (ie internet) reliably deliver messages, implement reliable + large messages w transport layer by sending multiple unreliable messages

Network Limitations
* "best-effort" service
* Message Lost:
  * Message sent by A to B, but message never arrives at B
    * A knows it sent, but B doesn't, so A and B don't know that it didn't arrive
  * Solution:
    * Acknowledgements: B sends response if message received 
    * WHICH MEANS: if A doesn't get a response back within a certain time, then resend message
* Lost Acknowledgement:
  * A sends message to B, B responds w acknowledgement but that doesn't go through
  * Q: how to fix this?
  * A:
    * A set timeout, then resend message; B needs to handle receiving message twice, but sockets you get data once
* Delayed Message:
  * A resends after timeout, but B responded already, now responds again, so they both receive the same message twice because can't tell the response is slow
* Delayed Acknowledgements:
  * B has a timeout 

If only want to send one message, acknowledgements and messages are enough. But, bigger messages, that's not enough ...

Splitting Messages
* Resend?
  * Problems:
    * First acknowledgement lost, then get duplicated things, so end up with a reconstructed message
* Added sequence number
  * Missed ack --> A resend --> Now B responds and they have a reconstructed message that's just the original message
  * If A gets a duplicate ack, they know that B hasn't received next message
  
Message Corrupted
* Message received != bits sent
* Corruption: ie bit flip
* Send "message" + checksum
* Receiver computes checksum with the same data
  * ? doesn't this require that the sender and receiver communicate some way outside of these messages to determine the checksum alg?
* Checksum: some calculation using the OG message
  * ie: parity

Going Faster
* sending one message, waiting for acknowledgement, etc is SLOW
* "best effort" --> Transmission Window
* Transmission Window: Send a window of parts speculatively, then wait for ACKs

## Protocols Reading

### DHCP

Flow: DORA
1. Discover
2. Offer
3. Request
4. Acknowledge

Model:
0. Your computer does NOT have an IP address yet, so it uses:
   1. source IP = 0.0.0.0 (meaning "I don't have one yet)
   2. destination IP = 255.255.255.255 (broadcast = "everyone on this network")
1. Discover: client --> everyone
   2. "Is there any DHCP server out there"
   3. Sent as:
      4. UDP port 68 --> 67
      5. IP: 0.0.0.0 --> 255.255.255.255
      6. includes MAC address aka hardware ID
   7. broadcast because the client doesn't know who the server is yet
   8. MAC address because it's the only unique identifier it has
9. Offer (server --> client)
   10. "yes! here is an IP address you can use"
   11. server responds with:
       12. an available IP address
       13. still uses broadcast because the client doesn't officially own the IP yet
   14. its possible that multiple servers could respond but usually only one does (if multiple did that would be handled in the next step)
15. Request (client --> chosen server)
    16. "I accept that specific IP address"
    17. sent as:
        18. UDP 68 --> 67
        19. IP: 0.0.0.0 --> server IP
    20. client only picks one offer which prevents multiple servers from assigning conflicting addresses
21. Acknowledge (server --> client)
    22. "confirmed -- that IP is officially yours"
    23. server finalizes the assignment
    24. now the client:
        25. can use the IP
        26. can start normal communication (like DNS, HTTP, etc)

Since IPv6 is so big, typically the server will give part of an address and the server on the local network will fill in the rest because its easier to decentralize
this is called: SLAAC