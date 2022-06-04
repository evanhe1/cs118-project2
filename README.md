Evan He evanhe27@gmail.com 705-619-333

Seth Carlson scarlson896@gmail.com 605-600-334

# CS118 Project 2

This is the repo for spring 2022 cs118 project 2.

## Makefile

This provides a couple make targets for things.
By default (all target), it makes the `server` and `client` executables.

It provides a `clean` target, and `tarball` target to create the submission file as well.

You will need to modify the `Makefile` USERID to add your userid for the `.tar.gz` turn-in at the top of the file.

## Academic Integrity Note

You are encouraged to host your code in private repositories on [GitHub](https://github.com/), [GitLab](https://gitlab.com), or other places.  At the same time, you are PROHIBITED to make your code for the class project public during the class or any time after the class.  If you do so, you will be violating academic honestly policy that you have signed, as well as the student code of conduct and be subject to serious sanctions.

## High Level Design

### Client
After establishing the connection to the server, the client runs an infinite loop that can checks for three events during each iteration. The first event is receiving a packet from the server, which would trigger an increment of the base of the pkt window based on the ack number of the packet received. The second event is a timeout, which triggers a retransmission of all unacknowledged packets. The third event is the presence of newly available space in the window, which would trigger the transmission of new packets to fill this space. Once all packets have been acknowledged by the server, the client will break out of the while loop and initiate connection teardown. 

### Server
After establishing the connection with the client, the server runs an infinite loop that serves only one general purpose: to acknowledge incoming packets from the client. The server will respond to new packets and retransmissions differently, only performing file writes upon receiving new packets. Another explicit condition checks for the arrival of a FIN packet so that the server can continue the connection teardown process.

## Problems and Solutions

The most significant problem encountered during the development was correctly handling the wraparound of both sequence numbers and window indices (specifically the base, nextseqnum, baseWindowIndex, and nextWindowIndex variables). Carelessness with these values led to many infinite loops within the program. Most of these difficulties were resolved by making sure to apply the modulo operator whenever incrementing any value associated with sequence numbers or window indices. However, a bug persisted in the client in which the while loop would deadlock whenever 10 packets were all acknowledged simulaneously. Since baseWindowIndex would equal nextWindowIndex in this case, it was impossible to distinguish this scenario from the more common case of there being no more room in the window. This problem was ultimately resolved with a key variable that was set to 1 when 10 packets were acknowledged simultaneously, which would trigger 10 new packet transmissions and break the deadlock, before being set back to 0 for future reuse. 

## Outside Sources Used
Computer Networking A Top Down Approach by Kurose and Ross