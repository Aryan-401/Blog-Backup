## API Avalanche — All about APIs

An API, better known as an Application Programming Interface, is a piece of such distinct parts distinct pieces of software to contact each other, often to exchange data using well-defined protocols. Our modern technological lifestyle often depends on APIs a lot. From sending a text through RCS (Rich Communication Service) to looking up the weather on Google, APIs do the heavy lifting. 

## How do APIs Work?

API architecture is usually explained in terms of client and server. The application sending the request is called the client, and the application sending the response is called the server. APIs are further categorized as shown below:

#### SOAP APIs 
These APIs use the **S**imple **O**bject **A**ccess **P**rotocol. Client and server exchange messages using XML. SOAP is a less flexible API that was more popular in the 90s and early 2000s.

#### RPC APIs
These APIs are called **R**emote **P**rocedure **C**alls. The client completes a function (or procedure) on the server, and the server sends the output back to the client.

#### Websocket APIs
Websocket API is another modern web API development that uses JSON objects to pass data. A WebSocket API supports two-way communication between client apps and the server. The server can send callback messages to connected clients, making it more efficient than REST API.

#### REST APIs
These are the most popular and flexible APIs found on the web today. The client sends requests to the server as data. The server uses this client input to start internal functions and returns output data to the client.

## The Public/Private Duality


![e5ee272c-dfe1-40e5-b66b-b0a36e815254.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1656925881902/xvZpromxg.png align="center")

Like many services, some APIs are in the private domain. They are used as proprietary services between different departments in the same organization. These APIs are out of the reach of the average developer, and there is little you can do about it.

Public APIs on the other hand are readily available to everyone. While some of them are free to use, others have a cost associated with every API call. They might not have authorization tokens as a feature.

## Planning for the Future

In the coming weeks we'll be looking at some of the major APIs you can use to build projects and end it with us building a very simple API using Flask. Hope you stay along for the ride. Be sure to look through the entire Series