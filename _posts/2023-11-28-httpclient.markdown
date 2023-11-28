---
layout: post
title:  "Sockets are exhausting"
date:   2023-11-28 17:00:00 +0000
---

## The problem 
A few years ago, I was a member of a software development team which was about to develop a software solution that would impact the lives of many people living within Northern Ireland. This software solution when released would allow people to gain access to bars and restaurants and board flights during a busy Christmas period after a period of lockdowns due to COVID-19. 

Our development team were anticipating this web application to be handling large scale traffic loads and to be firmly in the public eye for several months.  

The high traffic loads on our prospective web application concerned me, as these were demands we as a software development team didn’t previously have to consider when architecting our solutions. To ease my concerns, I started investigating our proposed solution for possible unforeseen issues, focusing upon issues that could arise due to high traffic.  

It was during this investigation I stumbled upon something that really surprised me -> as a team we had been using the HttpClient class incorrectly for several years.

## What is the HttpClient class 

When an application needs to communicate with a third-party service such as an API endpoint, it does so via a HTTP Request. 

>HTTP requests are a method of exchanging data on the internet between a client and a server’ 

The HttpClient class provides a method of sending HTTP requests and receiving HTTP responses from a resource defined within a URL property of the class (I.e. WeatherForecastAPI). This class tends to be the primary tool used by developers when working with Web Api's. 

According to Microsoft, the HttpClient is ‘a collection of settings which are applied to all requests executed by that instance’. 

This HttpClient class implements the Idisposable interface, this disposable interface states how a class manages the release of unmanaged resources within a C# application. Its purpose is to prevent memory leaks within your .NET Core application. 

As developers we are taught, when working with an idispoable instance to use a using statement to ensure the instance is correctly disposed after use. 

##### An example of a typical using statement

    using System;
    using System.IO;

    var line;

    using (StreamReader reader = new StreamReader("file.txt"))
    {
        line = reader.ReadLine();
    }

    Console.WriteLine(line);

However, the HttpClient class is an exception to this rule, as when the client is disposed of by the garbage collector, the underlying HttpSocket is not immediately released and remains in temporary idle state.

This socket idling can then result in HttpSocket exhaustion problems which can destabilise your web application. Socket exhaustion is not the only issue which can arise from an incorrectly utilised HttpClient. DNS related issues can also be issues introduced within your application by a poorly configured HttpClient. 

## Replicating Http Socket Exhaustion
In the following section I will demonstrate how the combination of HttpClient and using statements introduces socket exhaustion within our application. The following code is enough to demonstrate the underlying issue with the HttpClient class when incorrectly purposed. 

![Alt text](/docs/assets/httpclient.png)

We start this code segment by defining our http client within a using clause. We wrap our httpClient in a using statement as stated earlier, this is a very common approach within .Net development. 

Once initiated, we then define the parameters for our http client and create our request, finishing by reading the results.  
 
If we execute our application, this code will run fine and will return a 200 OK result to the console. In order to properly understand our misuse of the HttpClient within our example we must trace the clients use of the network connections. 

On the Windows platform, Netstat is a popular tool for studying network statistics. Once executed the Netstat tool will display all active network connections as well as some advanced information regarding that connection. 

As shown below, if when we execute the Netstat tool for our target API address we can view all of the network connections for this address. 

![Alt text](/docs/assets/httpclient1.png)

As you can see in the diagram above, when we send a http request, our application creates a new network connection. Regardless of our ‘clean coding’ by wrapping the HttpClient within a using clause, a network connection idlily persists in a “time-wait” state, with holding a network socket. 

“A time-wait state highlights that the network connection between the two entities is closed on one side. However, the connection will remain open connection state in case any packets of data are late to arrive, due to an unforeseen network connectivity issue.” 

So the HttpClient class will not “lock the front door” until it is certain all packets have arrived home from their journey.  

After a period of time, these time wait connections will eventually close. However, as you can see from the screenshot above we only have a certain number of connection pools available. In a high traffic, our web application will continually raise http socket exhaustion exceptions as our application struggles to deal with the HTTP connection pool shortage. 

## Singleton pattern 

In the previous approach, we created a new instance of the httpclient within a using clause. The takeaway from this investigation is this approach creates a new network connection with every new instance of the HttpClient created. 

So rather than creating a new HttpClient for every request generated within our application, why not implement the singleton pattern and promote reuse within the application – this should resolve our issue? 

![Alt text](/docs/assets/httpclient.png2.png)

In this approach we can create only a single HTTP client and one network connection which is then maintained for the lifespan of the application. 

This approach is perfectly valid until there is any changes regarding the DNS or networking of our API. If a change does occur, our web application will then require a scheduled restart of the web application in order to obtain the latest configuration. 

## HTTP Client Factory 

We have identified the issues with incorrect use of the HTTP Client class and the problems with implementing the singleton pattern. In order to resolve these problems within our web application, we can use the IHttpClient factory, creating a new instance of the HttpClient class from within this factory. 

![Alt text](/docs/assets/httpclient3.png)

In the code example above, we have dependancy injected the IHttpClientFactory within the constructor for our class. In order to enable this dependancy injection, we have to add the following code within startup.cs class 

- services.AddHttpClient();

Enabling the IHttpClientFactory within our web application will introduce a number of benefits. Notably the client factory will manage the lifespan of the HttpMessageHandler which will prevent HTTP socket exhaustion. The client factory will also ensure the connections are reused from the connection pool and automatically disposed to avoid our DNS issues highlighted earlier. 