** Week 12 -- Networking 2 and Secure Channels **

## Content Overview

### Networking
* Protocols
  * ie: a frame
    * layers (link, network, transport)
  * IP
    * addresses (v4, v6)
  * port numbers and UDP
  * mapping b/w addresses and names/other addresses:
    * DNS
  * (briefly) HTTP, network address translation

### Secure Channels 1
* cryptography
  * symmetric encryption
  * asymmetric encryption
  * digital signature
* replay attacks 
* public key infrastructure

## Quiz

Consider the following incomplete code that uses monitors:

![alt text](q12_code.png){size=large}

What's the goal of the code?
1. AddMessage should: 
   1. {{ Take the mailbox and the new message as input }}
   2. {{ lock the mailbox }}
   3. {{ check to make sure the max number of messages is not exceeded (and handle that) }}
   4. {{ add the new message to the mailbox and increase the number of messages }}
   5. {{ wake up the sleeping threads to let them know a new message has arrived }}
   6. {{ unlock the mailbox }}
2. FirstMessageFrom should:
   1. checks the sender of each message to see if it matches the inputted person
   2. if no message from that sender exists, returns -1 (which will function as the cv for WaitForMessageFrom)
3. WaitForMessageFrom should:
   1. lock the mailbox 
   2. check whether there is already a message from who 
   3. if not, sleep on a condition variable 
   4. wake up when someone adds a message 
   5. re-check the condition 
   6. return the index of the first matching message

Question 1 (4 points) (see above)
Given the code above, what would make the most sense to place in BLANK 1?


1. box->cv = true; {{ pthread_cond_t isn't a boolean, you don't set condition variables; they're wait/notify mechanisms, not flags }}


2. message->cv = box->cv = true; {{ Same issue: condition variables are not values
Also: no one is waiting on message->cv anyway }}


3. pthread_cond_broadcast(&box->cv); {{ yezzz }}


4. pthread_cond_broadcast(&message->cv); {{ Threads are waiting on box->cv, not per-message CVs
No code ever waits on message->cv
👉 dead signal — wakes nobody }}


5. pthread_cond_signal(&box->cv); {{ this only wakes one thread, and if this message's from isn't who the thread is waiting on, then it'll go back to sleep, so broadcast is safer }} 


6. pthread_cond_signal(&message->cv); {{ again, threads are waiting on box->cv, not per-message CVs
No code ever waits on message->cv }}


7. pthread_cond_wait(&box->cv, &box->lock); {{ this is for waiting, not notifying }}


8. pthread_cond_wait(&message->cv, &message->lock); {{ same as the other wait option }}

ANSWER: {{ pthread_cond_broadcast(&box->cv) }}
Reasoning: {{ AddMessage should wake up threads waiting for new messages. Broadcast is used over signal because different threads may be waiting for messages from different people, so if only one thread wakes up, it may be waiting for the wrong sender and go back to sleep, meaning the signal is lost }}

Question 2 (3 points) (see above)
Given the code above, what would make the most sense to place in BLANK 2?


1. box->cv {{ Condition variables are not boolean conditions
They don’t represent “state” }}


2. !box->cv {{ same issue as above }}


3. box->messages[FirstMessageFrom(box, who)]->cv {{ this might result in indexing into messages with a -1 value, also, using a cv as a boolean which is wrong }}


4. FirstMessageFrom(box, who) == -1 {{ YYAYAYAYAYYYAY }}


5. FirstMessageFrom(box, who) != -1 {{ this is would be waiting while a message exists which makes no sense }}


6. pthread_cond_broadcast(&box->cv) {{ action, not conditions, so don't work in while }}


7. pthread_cond_signal(&box->cv) {{ again, action, not conditions, so don't work in while }}


8. pthread_cond_wait(&box->cv, &lock) {{ again, not a condition }}


9. pthread_cond_wait(&box->messages[FirstMessageFrom(box, who)]->cv, &lock) {{ and again, not a condition }}


10. pthread_mutex_unlock(box->lock); {{ not a condition }}

Answer: {{ FirstMessageFrom(box, who) != -1 }}
Reasoning: {{ waiting thread should keep waiting while there is no message from who }}

Question 3 (3 points) (see above)
Given the code above, what would make the most sense to place in BLANK 3?


1. pthread_cond_wait(&box->cv, &lock); {{ ok yes but also j bc the code is wrong so sort of but &lock isn't a local variable in the code and it should be otherwise this should be &box->lock }}


2. pthread_cond_wait(&box->messages[FirstMessageFrom(box, who)]->cv, &lock); {{ this could result in an invalid index, also threads aren't waiting on per-message CVs so it's the wrong abstraction }}


3. pthread_mutex_unlock(&box->lock); pthread_mutex_lock(&box->lock); {{ This is a busy wait
Does NOT block → wastes CPU
Does NOT coordinate with signal/broadcast }}


4. pthread_mutex_unlock(&box->lock); pthread_cond_wait(&box->cv, &lock); pthread_mutex_lock(&box->lock); {{ WRONG because:
pthread_cond_wait already:
releases the lock
sleeps
re-acquires lock
👉 doing it manually breaks correctness }}


5. pthread_cond_broadcast(&box->cv); {{ for producers not consumers }}


6. pthread_cond_signal(&box->cv); {{ ditto, wrong direction }}

Answer: {{ OK the answer key says: pthread_cond_wait(&box->cv, &lock);, but chat says that only works if there is a local variable named lock and there is not, so it should be: pthread_cond_wait(&box->cv, &box->lock); }}
Reason: {{ inside the loop, the thread should wait on the mailbox condition 

Question 4 (4 points)
Suppose a router using network address translation has the following table in place:


| remote address:port | public local IP port | private local IP address:port |
|---------------------|----------------------|-------------------------------|
| 128.165.17.3:22     | 23423                | 192.168.1.7:55938             |
| 128.101.45.111:443  | 23424                | 192.168.1.8:40180             |
| 128.101.45.111:443  | 23425                | 192.168.1.5:51491             |

Assume this is on a router with an IP address of 1.2.3.4 on the public network and 192.168.1.1 on a private network. The router assigns public IP ports starting at 23420.

If a packet comes in from the private network to the router with a source address of 192.168.1.5, source port of 800, and a destination address of 128.101.45.111, destination port of 22, the router should update its table by ____.


1. Adding (1.2.3.4:22, 800, 192.168.1.5:22) to the table.

{{ destination and source port are incorrectly the same, 
original source port used as the public local IP port 
which is wrong; also router IP address is used as the 
destination address which is also wrong }}

2. Adding (1.2.3.4:22, 22, 192.168.1.5:800) to the table.

{{ wrong to be using port 22 as public port
public ports are assigned by router
NOT equal to destination port }}

3. Adding (128.101.45.111:22, 800, 192.168.1.5:22) to the table.

{{ wrong to use port 800 (og source port) as public port; also 
swapping ports incorrectly;
NAT keeps:
private source port unchanged
assigns new public port }}

4. Adding (128.101.45.111:22, 22, 192.168.1.5:800) to the table.

{{ wrong to be using port 22 as public port
public ports are assigned by router
NOT equal to source port }}

5. Adding (128.101.45.111:22, 23426, 192.168.1.5:800) to the table.

{{ YAYAYYYAAYAYAYYAYAYYAYAYAYYYAAYAYAYYAYAYYAYAYAYYYAAYAYAYYAYAYYAYAYAYYYAAYAYAYYAYA }}

6. Adding (128.101.45.111:22, 800, 192.168.1.5:23426) to the table.

{{ wrong to use port 800 (og source port) as public port;
also swapping ports incorrectly;
NAT keeps:
private source port unchanged
assigns new public port }}

7. Doing nothing as the necessary entry already exists.

{{ it doesn't. :///// }}

Answer: {{ Adding (128.101.45.111:22, 23426, 192.168.1.5:800) to the table. }}
Reason: {{ Keeping the ports private to each IP address so that the public shared key doesn't reveal any private information }}

Question 5 (4 points)
Consider a network like what we discussed in class, except it never drops any packets. In this network, what does a reliability protocol need?


1. A reliability protocol needs acknowledgements, sequence numbers, timeouts, and checksums.


2. A reliability protocol needs sequence numbers, timeouts, and checksums, but not acknowledgements.


3. A reliability protocol needs timeouts and checksums, but not acknowledgements or sequence numbers.


4. A reliability protocol needs sequence numbers and checksums, but not acknowledgements or timeouts.


5. A reliability protocol needs timeouts, but not acknowledgements, checksums, or sequence numbers.


6. A reliability protocol needs sequence numbers, but not acknowledgements, checksums, or timeouts.


7. A reliability protocol needs acknowledgements and timeouts, but not checksums or sequence numbers.

Answer: {{ A reliability protocol needs sequence numbers and checksums, but not acknowledgements or timeouts. }}

Reason: 
{{ you don't need:
1. acknowledgements -- only need to confirm delivery, but if no loss, then no need to confirm
2. timeouts -- used to detect loss, but if no loss, useless

you will need:
1. checksums -- corruption may still exist
2. sequence numbers -- reconstruct correct stream if there are delayed packets
}}


Question 6 (4 points)
A new reliable transport protocol is designed to run on systems without the ability to set a timer for a timeout, but uses the other standard reliability mechanisms. Machine A and Machine B use this new reliable transport protocol. Machine A wants to send 100 unique data packets to Machine B. While using the new protocol, the network drops one packet about halfway through the 100 packets.

Which are true about this scenario? Select all that apply.


1. The communication has failed because Machine B will not have the contents of the missed packet and Machine A has no way to resend.


2. As long as the reliable transport protocol uses checksums, Machine B will know the dropped packet is lost.


3. As long as the reliable transport protocol uses sequence numbers, Machine B will know which packet is lost.


4. Machine A can know to resend the lost packet if Machine B sends acknowledgements without sequence numbers. a Machine A needs to send acknowledgements to confirm acknowledgements from Machine B due to the lack of timers.


5. The reliable communication protocol can ensure Machine B receives all packets in this case with only one packet re-sent.


6. Machine B must send all acknowledgements twice due to the lack of timers.

Answer: {{ 
* the protocol needs sequence numbers so B can detect delayed or dropped packets 
* the one packet re-sent thing is possible if that packet isn't lost, and in that case A may have to send more than one or something else etc
}}