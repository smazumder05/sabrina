Sabrina
=======

A client/server library that supports multiple protocols and provides a simple abstraction for efficiently communicating over TCP or UDP

###Overview


Sabrina is a Java library that provides a clean and simple API for efficient TCP and UDP client/server network communication using NIO. Sabrina  uses the Apache Avro serialization library to automatically and efficiently transfer object across the network. However any other serialization libraries can be plugged in. 

JSON Serialization is provided which uses [JsonBeans](http://code.google.com/p/jsonbeans/) to do serialization using JSON.

Sabrina also provides a bounded queueing model for handling a high volume of requests, this is based on the [Disruptor](https://github.com/LMAX-Exchange/disruptor)  

Sabrina is ideal for any client/server application. It is very efficient and is meant for high performance servers. It is being developed to support a mobile application which will be sending requests from various mobile devices.


A pure Scala version is also in the works.

[Running a server](#running-a-server)

This code starts a server on TCP port xxxxx and UDP port yyyyy:

```java
    Server server = new Server();
    server.start();
    server.bind(port_number);
```

The `start` method starts a thread to handle incoming connections, reading/writing to the socket, and notifying listeners.

This code adds a listener to handle receiving objects:

```java
    server.addListener(new Listener() {
       public void received (Connection connection, Object object) {
          if (object instanceof HelloWorldRequest) {
             HelloWorldRequest request = (HelloWorldRequest)object;
             System.out.println(request.text);
    
             HelloWorldResponse response = new HelloWorldResponse();
             response.text = "Thanks";
             connection.sendTCP(response);
          }
       }
    });
```
	 
Note the Listener class has other notification methods that can be overridden.

Typically a listener has a series of `instanceof` checks to decide what to do with the object received. In this example, it prints out a string and sends a response over TCP.

The HelloWorldRequest and HelloWorldResponse classes are defined like this:

```java
    public class HelloWorldRequest {
       public String text;
    }
    public class HelloWorldResponse {
       public String text;
    }
```
### Connecting a client

This code connects to a server running on TCP port_number:

```java
    Client client = new Client();
    client.start();
    client.connect(5000, "192.168.0.4", port-number);
    
    HelloWorldRequest request = new HelloWorldRequest();
    request.text = "Here is the request";
    client.sendTCP(request);
