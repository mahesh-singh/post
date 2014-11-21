# Rabbit MQ : Understanding message broker

##Where is the problem?
Our new customer just started sprint and the first story which we are delivering is registration module. We are re-writing this module from scratch.

[Enter mock-up]

Developers put their best effort to finsh entire module. Every thing was great until copule of change request comes.

1. Welcome email message must sent after the successful registration. Third party SMTP server must used to send the required messages. 
2. Registration trough diffrent affiliate link and track those registration through third party API to compare the affiliate performence. 
3. Log audit information about user like registration like time, location etc  

Everything was done, tested and deployed. 

Your code will look likes

[Sample code]

New shiny registration module went live and work like a charm. Suddenly registration module show error once user finish the registration. Investigation shows that third party SMTP APIs were down for some time. During the downtime user registered but never received confirmation emails.

Also amuse what if
- Third party API stops working
- Audit log table get locked due to high tragic
- You asked to send user's information to other internal system which are written in different language.
- Any many more such cases where direct communication required between applications

##Can our code scale to new requirement or remove dependency of third party APIs?


##What is messaging
A way of exchange messages from point A to point B or many points C.
Its enables distributed communication that is loosely coupled. Message sender  sends a message to a **destination**, and the message recipient can retrieve the message from the **destination**. However, the sender and the receiver do not have to be available at the same time in order to communicate.
To understand better email is a great example of messaging which enables people to communicate
People use messaging for scale the applications. 
You want to use messaging
- Send data to many applications without calling their API directly in your application. 
- Want to do things in certain order like transactional system.
- Monitor data feeds like number of registration in application

The component which receive the message from sender and recipient retrieve the message from, called **message broker** or **messaging middleware**.

##Message broker/middleware
According to wikipedia

>Message-oriented middleware (MOM) is software or hardware infrastructure  supporting sending and receiving messages between distributed systems. MOM allows application modules to be distributed over heterogeneous platforms and reduces the complexity of developing applications that span multiple operating systems and network protocols.

Message broker does many things
- Decouple message publisher and consumer
- Store the messages
- Routing of message
- Monitoring and management of messages

Historically it lacks standers and existing commercial implementations have proprietary  implementation and APIs. Which normally have difficulty in integration with different systems and not available on all platforms.

ASMQ (Advanced Message Queuing Protocol)  is an open standard application layer protocol for message-oriented middleware.

From the AMQP website:

>AMQP is an Open Standard for Messaging Middleware.

>By complying to the AMQP standard, middleware products written for different platforms and in different languages can send messages to one another. AMQP addresses the problem of transporting value-bearing messages across and between organisations in a timely manner.

>AMQP enables complete interoperability for messaging middleware; both the networking protocol and the semantics of broker services are defined in AMQP. 

In nullset AMQP defines
- Where to send messages (Routing)
- How to get there (Delivery)
- What goes in must come out (Fidelity) 

AMQP is standard(http://en.wikipedia.org/wiki/Advanced_Message_Queuing_Protocol), wire level protocol (communicating with a remote machine or  getting data from point to point http://en.wikipedia.org/wiki/Wire_protocol) and have many implementations(http://en.wikipedia.org/wiki/Advanced_Message_Queuing_Protocol#Implementations).

**RabbitMQ** is open source message broker software that implements the Advanced Message Queuing Protocol (AMQP). 

##Hello RabbitMQ
Rabbit MQ is nothing more than a message broker. All it does take messages and send to other places in pretty smart way. AMPQ is the protocol that it implements. Its completely language neutral you can write to them and read to them in any language just like you are using TCP or HTTP.
It runs on all major operating system (http://www.rabbitmq.com/platforms.html) and support huge number of developer platforms like Java, .NET, Python, PHP, Erlang and many more (http://www.rabbitmq.com/devtools.html).

RabbitMQ server is written in the *Erlang* programming language and is built on the *Open Telecom Platform*(OTP) framework for clustering and failover.

From Erlang website

**What is Erlang?**
>Erlang is a programming language used to build massively scalable soft real-time systems with requirements on high availability. Some of its uses are in telecoms, banking, e-commerce, computer telephony and instant messaging. Erlang's runtime system has built-in support for concurrency, distribution and fault tolerance.

**What is OTP?**
>OTP is set of Erlang libraries and design principles providing middle-ware to develop these systems. It includes its own distributed database, applications to interface towards other languages, debugging and release handling tools.

WhatsApp uses Erlang to run messaging servers, achieving up to 2 million connected users per server.

RabbitMQ have following advantages
- Fast
- Polyglot
- Simple management
- No Erlang knowledge needed
- Great documentation and community
- And many more 

## Installation
**Erlang:** Download and install Erlang (OTP R16B03-1 Windows 64bit Binary Release) from http://www.erlang.org/download.html

**RabbitMQ:** Download and install RabbitMQ server for window from http://www.rabbitmq.com/download.html 

RabbitMQ will be installed as a windows services

RabbitMQ also comes with web based management plugin which is quite handy to manage RabbitMQ.
Steps to install management plugin  
- Open command prompt
- Run following command from <RabbitMQ installation folder>/sbin
    `rabbitmq-plugins.bat enable rabbitmq_management`
- Restart the RabbitMQ by following command
    `rabbitmq-service.bat stop`
    `rabbitmq-service.bat start`
By default management plugin will run on http://localhost:55672/
Default user name and passowrd is guest & guest

##Basic operation 
-To stop RabbitMQ along with Erlang
    `rabbitmqctl stop`
- Stop/Start RabbitMQ only
    `rabbitmqctl stop_app`
    `rabbitmqctl start_app`


##Elements
Producer create message and send (publish) into message broker (RabbitMQ). Message must have to parts: a payloads and a label. Payload is data and it can be anything from a simple JSON to MPEG-4 file. Label describe the payloads and how rabbitMQ will determine who should get the copy of message. The communication between publisher and rabbitMQ is one directional and fire and forget.

![Alt text](https://github.com/mahesh-singh/post/raw/master/img/produce_consumer_rabbitmq.jpg)



###Exchanges
###Queues
###Binding

##Administering
##Summery
##Further Read

##Disclaimer
- I am not an Erlang programmer.
- I don't know everything
