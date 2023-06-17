# Backend Guide

[TOC]

### Roadmap

https://roadmap.sh/backend

### Channels

- Hussein Nasser

---

### OSI Model

Reference: [Link](https://www.youtube.com/watch?v=7IS7gigunyI)

OSI stands for Open Systems Interconnection Model. It is responsible for communication between systems. Internet, LAN, Router, Phone, everything that communicates in computer system uses OSI Model in one way or another. There are 7 layers through which data goes.

- When a request is made: it starts from Layer 7 and goes to Layer 1

- When server receives the request: it starts from Layer 1 and goes to Layer 7

**Explanation by example:** 

Step 1: A device makes a GET request to server

- LAYER 7 - **Application**: This is where a **client makes a request to server** device, such as a GET request. This request contains a whole bunch of information, such as **headers, cookies, content-type headers**, etc, which is summarized into a string, so the string can participate with the rest of the OSI model as byte data.
- LAYER 6 - **Presentation**: This layer does the job of **encryption** (depending on http and https). At this step data is encrypted. This is where you can get screwed if you are on public wifi...people can sniff your data.
- LAYER 5 - **Session**: Since there maybe several TCP connections to one servers at a time, with identical packet information from layers 1 through 4, we need a way to distinguish between then using session id to tag it.
- LAYER 4 - **Transport**: This layer further adds the network layer IP address into source and destination ports.
- LAYER 3 - **Network**: This layer adds more info to the segments, that is, destination IP address and the source IP address. It doesn't check for errors and sends the data blindly. 
- LAYER 2 - **Data Link**: Takes the packets and break them down and adds target MAC address (Media Access Control address. It is an identifier for a machine or network) for each of these smaller bits of packet that we call **Frames**. Sometimes we don't know the MAC address and that is handled by ARP(Address Resolution Protocol) that takes the IP address and reverse engineer it into the MAC address.
- LAYER 1 - **Physical**: Frames get converted into 1s and 0s. Then our device sends those 1s and 0s. And it has no idea where to send this data becoz it has no direction (light has no direction). Since electrical signals travel in all directions in a network, the data frames reach all devices in the network. Once the data frame is able to identify that the device is not the intended destination, the frames drop. In an unsecured network, this is where a malicious application can choose to not drop the frames, and steal the data. Now data link layer checks if the frame was intended for the machine. This is also the place where we go from LAYER 1 to 7.

Step 2: A server receives the data

- LAYER 1 - Physical: Server receives the data (1s and 0s). Now it passes the data to Data Link Layer
- LAYER 2 - Data Link: This layer converts the data to Frames and checks the data on frames if the packet was intended for the machine or not. If it doesn't match, frame drops. If address matches, then it is passed to the next layer (This is done by network card in device). Before passing it to next layer, it removes extra headers from it (that this layers attached).
- LAYER 3 - Network: Now this gets the data with headers that it knows. It checks the IP address of reciever. If it is same as the receiver, it proceeds. It removes its headers and moves to the next layer
- LAYER 4 - Transport: It sees the data and finds the port and sees if we have an application running on that port. Then it removes its headers and moves to the next layer
- LAYER 5 - Session: It finds the session with Id tagged to it. This helps to identify the connection which made the request. Again removes the headers and pass to the next layer
- LAYER 6 - Decrypt: Takes the info and decrypts it and sends it to next layer
- LAYER 7 - Application: Here it receives the actual data i.e. GET request with headers attached to it. This is where our server responds to the requests (finally). 

> MAC address: Media Access Control address. It is an identifier for a machine or network

### TCP vs UDP

Reference: [Link](https://www.youtube.com/watch?v=qqRYkcta6IE&t=2147s)

TCP and UDP are communication protocols that allows us to send and receive data in a network. UDP and TCP are layer 4 transport protocols. They live in layer 4 of OSI model. Two important info that lives here are IP address and the port. 

##### TCP

It stands for Transmission Control Protocol. It was designed to send information from one server to another server using IP address and port.

**Pros:**

- Acknowledgement
- Guaranteed delivery
- Connection based
- Congestion control
- Ordered packets

**Cons:**

- Larger packets: due to adding extra headers
- More bandwidth
- Slower than UDP: due to waiting congestion control, ordering of package etc.
- Stateful
- Server memory: it has to allocate memory for connections
- DOS attack: becoz of the limit on number of connections

##### UDP

It stands for User Datagram Protocol. UDP doesn’t have the error checking and ordering functionality of TCP and is best utilized when error checking is not needed and speed is important. This built-in lack of reliability is why UDP is sometimes referred to as ‘Unreliable Datagram Protocol’. 

UDP is commonly used in time-sensitive communications where occasionally dropping packets is better than waiting. Voice and video traffic are sent using this protocol because they are both time-sensitive and designed to handle some level of loss

**Pros:**

- Smaller packets: becoz no headers and overheads
- Less bandwidth
- Faster than TCP: due to no waiting for congestion control and ordering of packets
- Stateless

**Cons:**

- No acknowledgement: doesn't tell if data was received or not
- No guaranteed delivery
- Connectionless: no physical connection, client doesn't know server and vice versa
- No congestion control: it keeps sending data, no waiting
- No ordered packets
- Security: Due to no connection it is not secure

### ACID in relational db

Reference: [Link](https://www.youtube.com/watch?v=pomxJOFVcQs&list=PLQnljOFTspQUNnO4p00ua_C5mKTfldiYT&index=13&t=67s)

ACID stands for Atomicity, Consistency, Isolation, Durability.

For this, first we need to understand **Transactions**

**Que.** What is a Transaction ?

**Ans.** A transaction is a collection of queries i.e. one or more queries to perform a task at hand. It is one unit of work. E.g. Deposit amount in an account (SELECT, UPDATE, UPDATE)

Transaction example: Send $100 from account 1($1000) to account 2($500)

Transaction:

1. Check if account one has balance greater than $100. 
   
   `SELECT balance FROM account WHERE id=1`

2. Now subtract $100 from account 1. 
   
   `UPDATE account SET balance = balance - 100 WHERE id=1`

3. Add the amount to account 2.
   
   `UPDATE account SET  balance= balance  + 100 WHERE id=2`

#### Atomicity

Atomicity of a transaction means that all the steps in a transaction must succeed and if one fails, then whole transaction should fail i.e roll back. 

In previous example, if we avoid the principle of atomicity, we will see that if our second update query fails, it won't roll back all the process. Thus, account 1 is debited but account 2 is not credited.

An atomic transaction is a transaction that will rollback all queries if one of the queries failed.

#### Isolation

Isolation of transaction means that one transaction cannot see changes made by other transactions. Some kind of isolation is always good in transactions otherwise it will result in unwanted and unexpected changes in inflight transaction.

Lack of transactions leads to **Read Phenomena** 

Read Phenomena:

- Dirty Reads: So we are in a transaction and some other transaction made changes to db. In our inflight transaction we now read that new data that is different from what we started our transaction with. This will lead to dirty reads. One main point is that other transaction that affect our inflight transaction is not still committed and may roll back in case of failure.
- Non repeatable reads: We are in a transaction and made our first query. But another transaction made change to our db (commit or not commit doesn't matter). Now in our inflight transaction we make the same query again and this time we get different result. This is called non repeatable reads.
- Phantom reads: This mostly happens when we are making range queries. We select a range of values from db in one transaction but another transaction added a new record to the db. Now in our inflight transaction we make another query where we select rows from db but this time we get newly  added records. This is called phantom reads and are hard to prevent.
- Lost updates: When you start to change something in a transaction and some other transaction over wrote your value before you even commit it. Then you are going to commit wrong value ie. you lost your update.

#### Consistency

Atomicity and Isolation helps us with consistency. 

There are two types of consistency: 

1. Consistency in data: Defined by user, e.g. defining table schema. Lack of atomicity leads to inconsistency. Isolation also help us in consistency. E.g Number of likes and likes stored in a table is not required to be consistent. 
2. Consistency in reads: 

### Publish-Subscribe Architecture

### TCP 3-way handshake

Scenario: we make a get request from a client to a server

Points:

- Every request is turned into packets and each packet is sent one after another. Order may vary
- Server receives those packets, assemble them, and read it to see yes it is get request

Problems in between the flow:

**Que: What if a packet didn't arrive ?  How does the client knows that a specific packet didn't arrive ?**

Ans: For this we introduce acknowledgement. It is send by server telling client that I have received this packet.

**Que: How do we identify packets ?**

Ans: Each packet has a unique identifier. It is called sequence number. It can start from any number. This sequence number helps server to arrange packets in order 

to be continued....

### Layer 4 & Layer 7 reverse proxies

layer 7 proxy takes the request from the client, do all the layer 1-7 things and then transfers to actual servers that process the request.

Layer 4 on the other hand takes the request not even request but a single packet and transfer it to actual server without even looking at the request. 
