# Rabbit MQ : Understanding message broker

##Where is the problem?
Our new customer just started sprint and the first story which we are delivering is registration module. We are re-writing this module from scratch.

![Fanout exchange in RabbitMQ](https://github.com/mahesh-singh/post/blob/master/img/mockup_registration.png)

Developers put their best effort to finish entire module. Every thing was great until couple of change request comes.

1. Welcome email message must sent after the successful registration. Third party SMTP server must used to send the required messages. 
2. Registration trough affiliate links and track those registration through third party API to compare the affiliate performance. 
3. Log audit information about user like registration like time, location etc  

Everything was done, tested and deployed. 

Your code will look likes

```
public void RegisterUser(User user)
{
    //Register user
    userService.Register(user);

    //Send welcome email
    emailService.SendWelcomeEmail(user)

    //Call third party api for affiliate tracking
    someThirdPartyWrapper.log(user)

    //audit log
    auditLogger.log(user)
}
```

New shiny registration module went live and work like a charm. Suddenly registration module show error once user finish the registration. Investigation shows that third party SMTP APIs were down for some time. During the downtime user registered but never received confirmation emails.

Also amuse what if
- Third party API stops working
- Audit log table get locked due to high tragic
- You asked to send user's information to other internal system which are written in different language.
- Any many more such cases where direct communication required between applications

##Can our code scale to new requirement or remove dependency of third party APIs?
Assume that if our RegisterUser method will only register the user. Other task can be performed by other applications without impacting the user registration.

Lets change the code

```
public void RegisterUser(User user)
{
    //Register user
    userService.Register(user);

   //Send message about user's registration
   var message = new Message()
   {
    //user's registration information
   }
   messagingService.SendMessage(message)
}
```

These message can be stored in central place so that other application can access. Now email service, third party APIs and audit log service can run independently and perform their task without impacting user registration module.
Even in case of any issues in email or third party service our registration module keep working and once these services up again they can continue their task without losing any data. 

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

>By complying to the AMQP standard, middleware products written for different platforms and in different languages can send messages to one another. AMQP addresses the problem of transporting value-bearing messages across and between organizations in a timely manner.

>AMQP enables complete interoperability for messaging middleware; both the networking protocol and the semantics of broker services are defined in AMQP. 

In null-set AMQP defines
- Where to send messages (Routing)
- How to get there (Delivery)
- What goes in must come out (Fidelity) 

[AMQP](http://en.wikipedia.org/wiki/Advanced_Message_Queuing_Protocol "AMQP")
 is standard, [wire level protocol](http://en.wikipedia.org/wiki/Wire_protocol, "wire level protocol") (communicating with a remote machine or  getting data from point to point ) and have many [implementations](http://en.wikipedia.org/wiki/Advanced_Message_Queuing_Protocol#Implementations).

**RabbitMQ** is open source message broker software that implements the Advanced Message Queuing Protocol (AMQP). 

##Hello RabbitMQ
Rabbit MQ is nothing more than a message broker. All it does take messages and send to other places in pretty smart way. AMPQ is the protocol that it implements. Its completely language neutral you can write to them and read to them in any language just like you are using TCP or HTTP.
It runs on all [major operating system](http://www.rabbitmq.com/platforms.html) and support huge number of developer platforms like [Java, .NET, Python, PHP, Erlang and many more](http://www.rabbitmq.com/devtools.html).

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

By default management plugin will run on http://localhost:15672/
Default user name and password is guest & guest

![Producer and Consumer in RabbitMQ](https://github.com/mahesh-singh/post/blob/master/img/rabbit_manag_plugin.png)



##Basic operation 
-To stop RabbitMQ along with Erlang

    `rabbitmqctl stop`
- Stop/Start RabbitMQ only

    `rabbitmqctl stop_app`
    `rabbitmqctl start_app`


##Elements
**Producer** create message and send (publish) into message broker (RabbitMQ). Message must have to parts: a payloads and a label. Payload is data and it can be anything from a simple JSON to MPEG-4 file. Label describe the payloads and how rabbitMQ will determine who should get the copy of message. The communication between publisher and rabbitMQ is one directional and fire and forget.

**Consumer** on the other hand attach to broker and subscribe to a queue to get the message.  

![Producer and Consumer in RabbitMQ](https://github.com/mahesh-singh/post/blob/master/img/produce_consumer_rabbitmq.jpg)

Your app can be producer when it needs to send message to another applications, or it can be consumer when it needs to receive the message. 

####Connect to RabbitMQ
Applications will connect to rabbitMQ by creating TCP connection and get authenticated. Setting up and tearing down TCP session is an expensive for OS. AMPQ have a concept of **channel** which is a virtual connection inside a real TCP connection. Publisher/Consumer apps use channel to issue AMQP command to broker. A single TCP connection can be use to establish multiple communictaion path between application and broker. Publisher writes to channel and consumrt read through channel.


###Exchanges, Queues and Binding
**Exchange:** Producer publish messages in exchange. Producer never directly communicate through consumer applications

**Queue:** Message end up in queue and receive by consumer.

**Binding:** Rule to route the message into one or more queue. This is relationship between exchange and a queue.

![hello world example  in RabbitMQ](https://github.com/mahesh-singh/post/blob/master/img/hello-world-example-routing.png)


When producer publish message in exchange, it contains two parts a payloads and a label. Label is called routing key. Each queue is bound to a routing key or a pattern of routing key. This routing key binding with queue are those rules for which exchange put message into queue.

Exchange receives messages from producer and the other side it pushes them to queues. The exchange must know exactly what to do with a message it receives. Should it be appended to a particular queue? Should it be appended to many queues? Or should it get discarded. The rules for that are defined by the exchange type.  

There are mainly four type of exchanges available: direct, topic, headers and fanout. This tutorial will focus on direct, topic and fanout exchange.

**Fanout Exchange:** It brodcast all the messges it recive to all the queues it knows. 

![Fanout exchange in RabbitMQ](https://github.com/mahesh-singh/post/blob/master/img/fanout_ex_rabbit.jpg)


**Direct exchange:**
As we have seen Fanout exchange broadcasts all messages to all queues. In direct exchange message goes to the queues whose binding key exactly matches the routing key (label) of the message. In direct exchange it is also perfectly legal to bind multiple queues with the same binding key.

In below example Q1 & Q2 is bind with routing key "Orange", Q3 with yellow and Q4 with Orange, black and green.

- Excannge receive message with "Orange"  routing key it will dilliver  message to Q1, Q2, and Q4 
- Excannge receive message with "Yellow" routing key it will dilliver message to Q3.
- Exchange receive message with "Green" routing key it will dilliver to Q4. 

![Direct exchange in RabbitMQ](https://github.com/mahesh-singh/post/blob/master/img/direct_exchange_Rabbit.jpg)

**Topic exchange:**
Direct exchange gives flexibility to bind routing key with queues, but still it lacks binding based upon pattern. In topic exchanges will route messages to one or many queues based on the pattern that was used to bind a queue to an exchange. Messages sent to a topic exchange can't have an arbitrary routing_key - it must be a list of words, delimited by dots. A valid routing key examples are "stock.usd.nyse", "nyse.vmw", "quick.orange.rabbit". Queues can bind with exchange bu using patterns like "*.usd.*" to get all the message where "usd" is a middle part of the message.

![Topic exchange in RabbitMQ](https://github.com/mahesh-singh/post/blob/master/img/topic_exchange_rabbitMQ.jpg)

In above example 
1. Q1 will get all the message which start with log. like log.debug, log.error, log.info and log.warn etc.

2. Q2 will get message with routing key log.error.

3. Q3 will only get log.info message.

4. Q4 will get all the message.


##Summery
##Further Read

##Disclaimer
- I am not an Erlang programmer.
- I don't know everything
