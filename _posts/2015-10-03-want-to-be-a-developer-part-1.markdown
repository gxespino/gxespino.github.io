---
layout: post
title: "So, You Wanna Be A Developer? - Part 1"
date: 2015-10-03 02:10:40
categories: Web Development REST CRUD RAILS
---

##How does your browser work?

I knew this question was coming and I even read a few blog posts and watched a few youtube videos on the topic to better prepare myself for this seemingly fundamental question. Yet as I fumbled through with a half baked response I realized, I still had no concrete idea of how the internals of a website and browser worked. The key words I could spit out were REST, HTTP, IP, DNS, CRUD, and the list goes on. But the second we started talking about the internals of a request and how a browser renders it all, I drew a blank.

During the entire interview my mind screamed "ask me about TDD and RSpec and how I implemented this ETL process using OOP and how I contemplated the template design pattern versus strategy." But what good are higher level concepts when you fumble on the fundamentals? And so if I'm to call myself a web developer I'd better learn the **internals of browser operations** so that I can formulate a better understanding, appreciation, and justification for the decisions I make down the line. So let's get down to it.

#####HTML & HTTP

Wikipedia defines HTML (HyperText Markup Language) as the standard markup language that is used to render web pages. Web browsers read and interpret HTML documents and render human-readable web pages as a result. HTTP (HyperText Transfer Protocol), on the other hand, is the application protocol that is used to transfer and exchange hypertext (oftentimes packaged up in HTML), among other things.

So let's piece this together. When you and I enter a web address into the browser and hit Enter, the first thing that happens is that the address is sent off to a system called the DNS, a comprehensive naming system for all the machines and computers connected to the internet. Each DNS server has a listing of machines connected to the internet and they are able to associate a web address to it's unique IP address. An IP address is simply an address that identifies a specific server connected to the internet. If the DNS system finds an IP address it sends it back to our web browser that in turn makes a request to that server at the IP address specified. Assuming everything went well, that server sends back a message saying everything is good and follows that up with the web page we requested. 

This image breaks down the process much better than I can: 

![browser](http://cdn.trendblog.net/wp-content/uploads/2014/02/how-do-dns-servers-work.gif)

Note that the first DNS server did not have the IP address for the web address (URL) and thus it passed on the request to another DNS server. This process repeats until a DNS server is found to have that specific URL cached.

#####Ok, So What's REST?

We hear the term thrown around a lot in web development and in the Ruby/Rails community: REST. So what is it and how does it relate to HTTP, or better yet *is it HTTP*? Well not exactly. REST (Representational State Transfer) is probably best described as a set of **rules that dictate how we should use HTTP**. When applied, REST dictates a set of desirable constraints that lead to a higher performing and more maintainable architecture. A system is considered RESTful when it follows the constraints laid out in REST. In essence, REST is a set of design principles that underpin HTTP, allowing it to be used in a diverse set of applications/devices with minimum overhead.

So, what are these architectural constraints dictated by REST and what are their specifics benefits? Well, I won't go into all of them since they extend the scope of this post, but for further reading you can visit [REST's wiki page](https://en.wikipedia.org/wiki/Representational_state_transfer). The important part to consider when talking about REST being applied to a modern web service (API, for example) is that: 

+ They utilize the standard HTTP methods (GET, POST, PUT, DELETE)
+ Communicate via an internet media data type (commonly JSON)
+ Utilize a base URI

#####Getting Closer. But You Lost Me at GET POST PUT DELETE?

Remember when I said HTTP is the application protocol that is used to transfer and exchange hypertext? Well HTTP does that by sending out requests between clients and servers with a specific *verb* that dictates the type of request and the desired action to be performed. Mouthful? Well it is but let's dive further. 

In the context of a RESTful web service we'll utilize four main HTTP verbs: GET, POST, PUT & DElETE. These four verbs are meant to greatly enhance what a specific request does. Generally the four verbs do as follows: 

+ **GET** - Read a specific resource or a collection of resources.
+ **POST** - Create a new resource. Also a catch-all verb for operations that don't fit into the other categories.
+ **PUT** - Update a specific resource or a collection of resources. Can also be used to create a specific resource if the resource identifier is known before-hand and there is no existing resource.
+ **DELETE** - Remove/delete a specific resource by an identifier.

In terms of a Rails application, these verbs can be utilized on a specific resource or a collection of resources. Let's review:

![rest](http://i.imgur.com/BoU537k.png)

As you can see, and this was admittedly confusing to me prior to this blog post, there are eight potential combinations of HTTP methods and URIs (entire collection or specific item with a collection). 

#####Let's Tie It All Together

When we are requesting a web page on our browser (the client), we send out a request to a DNS server for the specific server IP that hosts that web page. Once a DNS server is found to contain our requested web page, it sends us, the client, the associated IP address of that server. From there our browser, via HTTP, sends a request utilizing an HTTP verb to that server. That server then performs the requested action on the specific resource that we requested (e.g. GET a collection or specific item, POST a collection or specific resource, etc.). If our request is appropriate, the server sends back it's response, again via HTTP, with a message body that may or may not contain HTML. If the body contains HTML, our browser reads and inteprets it for us and renders the human-readable web page on our screen.

Good so far? It's a very top level view and a few steps could use further clarification. I also didn't touch on CSS and Javascript at all. 

I'll save all that for a later post while also diving more into how this all ties in Rails specifically. We'll tackle CRUD, routes, and MVC in part 2. 



