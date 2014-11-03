# Rabbit MQ : Understanding message broker

##Where is the problem?
Our new customer just started sprint one with TPG team and the first story which we are delivering is registration module. We are re-writing this module from scratch.
<Enter mockup>
Developers put their best effort to write entire module, used all necessarily design patterns. Every thing was great until product team ask for a change, that welcome email message must sent after the successful registration. Change  accepted and we used third party SMTP server to send the messages.

Suddenly we receive another change from sales team which just sign up with affiliate company which going to give us lots of registration. To track the registration through affiliate link, post registration an affiliate API must be called. Change accepted and registration module tested and ready to deploy.

Suddenly legal team comes and say we cannot launch until we maintain the audit log of user's registration like time, location, some other basic information. Change accepted again modify the code and logged all the information in audit table. We are ready to launch. 

Your code will look likes
<code>

Our new shiny registration module went live and lots of new customers are ready to sign up. Suddenly registration module show error after user finish the registration. Production team investigated and found third party SMTP APIs are down. Problem resolved at SMPTP provider but during that time we got the reistration but user never recevied confirmation emails.

Aslo asume what if
- Affiliate API stops working
- Audit log table get locked due to high trafic
- You asked to send user's information to other internal system which are written in diffrent language.
- Any many more cases 

##Can our code scale to new requirement or remove dependency of third party APIs?



##What is messaging
A way of exchange messages from point A to point B or many points C.
Its enables distributed communication that is loosely coupled. Message sender  sends a message to a destination, and the message recipient can retrieve the message from the destination. However, the sender and the receiver do not have to be available at the same time in order to communicate.
To understand better email is a great example of messaging which enables people to co
People use messaging for scale the applications. 
You want to use messaging
- Send data to many applications without calling their API directly in your application. 
- Want to do things in certen order like transactional system.
- Monitor data feeds like number of registration in application

The component which receive the message from sender and recipient retrieve the message from, called **message brocker** or **messaging middleware**.

##Message brocker/middleware
Accoring to wikipedia

>Message-oriented middleware (MOM) is software or hardware infrastructure  supporting sending and receiving messages between distributed systems. MOM allows application modules to be distributed over heterogeneous platforms and reduces the complexity of developing applications that span multiple operating systems and network protocols.

Message brocker does many things
- Decuple message publisher and consumer
- Store the messages
- Routing of message
- Monitoring and management of messages

Historically it lacks standers and existing commercial implementations have proprity implementation and APIs. Which normally have difficulty in integration with diffrent systems and not available on all platforms.

ASMQ (Advanced Message Queuing Protocol)  is an open standard application layer protocol for message-oriented middleware.

From the AMQP website:

>AMQP is an Open Standard for Messaging Middleware.

>By complying to the AMQP standard, middleware products written for different platforms and in different languages can send messages to one another. AMQP addresses the problem of transporting value-bearing messages across and between organisations in a timely manner.

>AMQP enables complete interoperability for messaging middleware; both the networking protocol and the semantics of broker services are defined in AMQP. 

In nullset AMQP defines
- Where to send messages (Routing)
- How to get there (Delivery)
- What goes in must come out (Fidelity) 

AMQP is Standerd(http://en.wikipedia.org/wiki/Advanced_Message_Queuing_Protocol), wire level protocol (communicating with a remote machine or  getting data from point to poin http://en.wikipedia.org/wiki/Wire_protocol) and have many implementations(http://en.wikipedia.org/wiki/Advanced_Message_Queuing_Protocol#Implementations).

**RabbitMQ** is open source message broker software that implements the Advanced Message Queuing Protocol (AMQP). 

##Hello RabbitMQ
Rabbit MQ is nothing more than a message brocker. All it does take messages and send to other places in pretty smart way. AMPQ is the protocol that it implements. Its completely language netural you can write to them and read to them in any language just like you are using TCP or HTTP.
It runs on all major operating system (http://www.rabbitmq.com/platforms.html) and support huge number of developer platforms like Java, .NET, Python, PHP, Erlang and many more (http://www.rabbitmq.com/devtools.html).

RabbitMQ server is written in the Erlang programming language and is built on the Open Telecom Platform framework for clustering and failover.

From Erlang website
What is Erlang?
>Erlang is a programming language used to build massively scalable soft real-time systems with requirements on high availability. Some of its uses are in telecoms, banking, e-commerce, computer telephony and instant messaging. Erlang's runtime system has built-in support for concurrency, distribution and fault tolerance.

What is OTP?
>OTP is set of Erlang libraries and design principles providing middle-ware to develop these systems. It includes its own distributed database, applications to interface towards other languages, debugging and release handling tools.

WhatsApp uses Erlang to run messaging servers, achieving up to 2 million connected users per server.

RabbitMQ have following advantages
- Fast
- Polyglot
- Simple management
- No Erlang knowedge needed
- Great documentation and community
- And many more 

## Installation


##Elements
Producer create message and send (publish) into message brocker (RabbitMQ). Message must have to parts: a payloads and a label. Payload is data and it can be anything from a simple JSON to MPEG-4 file. Lable describe the payloads and how rabbitMQ will determine who should get the copy of message. The communication between publisher and rabbitMQ is one directional and fire and forget.

<Img>


###Exchanges
###Queues
###Binding

##Administering
##Summery
##Further Read

##Declamer
- I am not an Erlang programmer.
- I don't know everything
