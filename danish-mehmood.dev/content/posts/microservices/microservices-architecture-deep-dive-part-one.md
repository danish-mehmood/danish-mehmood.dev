---
title: "Microservices Architecture Deep Dive: Part One"
subtitle: "What are microservices, what are their tradeoffs and how to decompose a monolith in to many microservices"
date: 2024-11-29T08:08:47+05:00
lastmod: 2024-11-29T08:08:47+05:00
draft: false
author: "danish mehmood"
authorLink: ""
description: "This blog series is all about microservices, we will start with simple definition of microservices and 
and go all the way to know how to implement them effectively and when do they make sense and when is it a stupid idea 
to implement microservices"
license: ""

images: ["images/posts/microservices/microservices_header.gif"]
featuredImage: "images/posts/microservices/microservices_header.gif"
featuredImagePreview: "images/posts/microservices/microservices_header.gif"
resources:
  - name: "featured-image"
    src: "featured-image.gif"
tags: ["microservices" , "distributed systems"]
categories: ["microservices"]


hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false
lightgallery: true
ruby: true
fraction: true
fontawesome: true
linkToMarkdown: true
rssFullText: false

toc:
  enable: true
  auto: true
code:
  copy: true
  maxShownLines: 50
math:
  enable: false
  # ...
mapbox:
  # ...
share:
  enable: true
  # ...
comment:
  enable: true
  # ...
library:
  css:
    # someCSS = "some.css"
    # located in "assets/"
    # Or
    # someCSS = "https://cdn.example.com/some.css"
  js:
    # someJS = "some.js"
    # located in "assets/"
    # Or
    # someJS = "https://cdn.example.com/some.js"
seo:
  images: ["images/posts/microservices/microservices_header.gif"]
  # ...
---

**Microservices** are one of the most popular methodology to develop and deploy the software systems. In popularity it is
only second to **Monoliths** (or Modular Monoliths).

This blog series is all about microservices, how do they differ from monoliths, what are their pros and cons, when do they
make sense and how to implement microservices effectively.

<!--more-->

{{< admonition note "Table Of Contents" >}}

:(fa-solid fa-arrow-right-long): Part 1 - **Microservices Architecture Deep Dive** :(fa-solid fa-arrow-left-long): you are here :(fa-solid fa-location-crosshairs):

:(fa-solid fa-arrow-right-long): Part 2 - [API Gateways and Backend For Frontend Pattern](https://danish-mehmood.github.io/microservices-architecture-deep-dive-part-two--api-gateways-and-bff-pattern/)

:(fa-solid fa-arrow-right-long): Part 3 - [Microservices Communication](https://danish-mehmood.github.io/microservices-architecture-deep-dive-part-three-communications/)

:(fa-solid fa-arrow-right-long): Part 4 - [Service Discovery](https://danish-mehmood.github.io/microservices-architecture-deep-dive-part-four-service-discovery/)
{{< /admonition >}}

## Microservices: The Inception

Microservices don't have any precise inception date, but rather evolved over time. It gained prominence around **2011**
largely due to shift towards cloud and distributed computing. following are the things which led to microservices

### Service Oriented Architecture (SOA[^1])

Microservices evolved as a refinement of SOA, which focused on creating loosely coupled, reusable services , but SOA had
some severe afflictions like heavy **middleware** and **complexity** to name a few.

### Cloud Computing

The growing adoption of cloud platforms like aws highlighted the need for smaller, more independent services (by the way
it was companies like aws which pioneered the breaking down of monoliths into smaller services)

### Martin Fowler and James Lewis's Definition

In 2014 in their influential article, titled ["Microservices: A Definition of This New Architectural Term"](https://martinfowler.com/articles/microservices.html), Fowler and Lewis codified the principles and patterns of microservices. This was pivotal in standardizing the concept and its terminology.

### Early Adopters And Case Studies

Companies like **Netflix** and **Amazon** implemented microservices early, demonstrating its effectiveness in scaling and adapting to dynamic workloads. Netflix's architecture shift started in the late 2000s and became a famous case study for microservices.

## Microservices: A Definition

**_While there is no precise definition of this architectural style, there are certain common characteristics around organization around business capability, automated deployment, intelligence in the endpoints, and decentralized control of languages and data._**

This is how martin fowler describes microservices, and in my opinion he is on point.

The goal of this blog post is to discuss these characteristcs that make up microservices that he is talking about. And in subsequent blog posts i will go deep in to the tools which the Engineers use to work with microservices effectively.

## Microservices: The Characteristics

### Preface

Microservices are independently releaseable services which are modeled around the business domain (e.g an ecommerce web application will have services like inventory, order management and shipping), each service exposes the API so that other services can use its functionality, but the implementation details remain hidden, once we have small services the more complex bigger system could be built using these small services like
a lego.
Information hidding[^2] is very important for microservices to work, every service hides as much information as possible from other services because that would make sure that in future if a microservice needs to change it can change without effecting the other services using this service which is subject to change.

### Independent Deployability

This refers to the characteristic of a service, in which you can change a service in some way and deploy it and the new feature or change is now available to the software's users, you don't need to change any other service, there are no cascading changes. some examples of changes would be , a small bug fix , api endpoint changes , addition of a whole new feature or a database schema change.

> Achieving this level of decoupling in a real world system is extremely difficult and engineers in the real world could relax this rule of microservices. nonetheless this is the **most important** characteristic of microservices, if you are able to achieve independent deployability for your application, you have already solved alot of problems which microservices promise to solve. And if you focus on this characteristic as the out come you would achieve numerous other benefits of microservices because independent deployability could only be achieved if you get alot of other things right.

### Services Owning there Own State (aka every service having its own database)

<!-- todo-add links to distributed transactions post -->

> This characteristic was synonymous to microservices few years ago , it went without saying that microservices should have their own databases each, but for the past few years it has become a point of contention, and people tend to challenge this rule more and more, the reason for contention is this that this rule does solve alot of problems but also create many of its own as well, like **Distributed Transactions**, which are very hard to get right.

This characteristic suggests that if service A needs some data from the database of some other microservice B, it should ask microservice B through its API to hand over the data and not share the database, this way the services will have control over what is visible to other services and what is not and access to data would be limited, and if the access to the data is limited by the API requests then service B can keep on changing e.g the schema of the database or even replace the whole database and jump from sql to no-sql database even than service A would not need to change because the API which service B is providing is the same.

If we want our services to be independently deployable we need to promote backward **compatibility** and limit the backward **incompatibility**,
Its that simple, but hard to achieve because you need to have a very vivid vision for the service you are designing, and you have to think in much advance what to expose and what not to expose to other services,

If you make some change to a service A which is backward **_incompatible_** with the services consuming service A , all the consuming services are forced to change, so **_"services owning their own state"_** is aiming to promote backward compatibility.

### What About Size

This is one of the most discussed topic in the microservices arena , **_what should be the size of a microservice?_**
obviously there could be many answers to this question, one answer which i have heard is **"this is something which should be subjective"**, I don't agree with this because if microservice's size is being determined by a person's (or multiple people's) subjectivity then there would be hundreds of engineers and project managers who would work on the microservice during its life time, whose subjectivity should the team adhere to.

there are other answers which do make sense like **"microservice should be as big as your head"** which means that microservice should be big enough so that a person working on it can fit it in his/her head, they should have complete idea how the microservice works, what are its interfaces, how is it internally coupled, whats exists where e.t.c. This definition of size is also somewhat contentious, because every person's ability to fit things in his or her head is different.

> this idea of a systems being as big as a persons head is not new, it was one of the goals of OOP as well, you design classes and encapsulate the functionality, exposing small interface of the class and giving class a name , now whenever the programmer needed to reason about the code in his head or in communication with someone else instead of mentioning the whole functionality and interface the person can alias it with the name of the class. **_interesting isn't it?_**

The Best definition of size would be, **"A microservice should have as small of an interface as possible"**, and this is very self-explanatory.
And this aligns with information hiding aspect i have just mentioned.

### Design For Failure

One characteristic that martin fowler lists is designing the services for failure, but what does this mean?

Well this means that at every point of a microservices design we should be **_assuming_** that the failure can happen and handle the failure accordingly. This is one of the side effects of breaking the monolith down into services, Because before the application was a **_single process_** monolith and now the monolith has become different services residing in different processes maybe on different computers and when they communicate failures could occur.

Whole projects and methodologies have emerged because of this, projects like **_grpc_** is a RPC[^3] framework which handles all the boilerplate for fault and error handling for the services, so you don't have to right the boilerplate and can focus on the logic of the service, and methodology like chaos testing has emerged to rigorously test the services in hostile circumstances.

### Smart Endpoints And Dumb Pipes

This characteristic suggests that, the communication channels between microservices should do the bare minimum which is **_communicate_** and thats it and all the other shenanigans should be left to the services which are communicating.

> This is a lesson we have learned from microservices predecessor **"Service Oriented Architecture"** which used ESB (Enterprise Service Bus) for communications which was a fully loaded communication system, and this ESB is what lead to the demise of SOA

REST is prime example of this, it is a very dumb protocol, service A sends request to service B, service B gets the response ready and sends back the response, plain and simple, REST does not introduce any kind of magic, it prefers being dumb and just gives us simple rules to abide by.

The messaging queues are another good example which contribute to the service communication without being too smart, they are simple queue datastructure implementation which come with certain guarantees and aid services with async communication.

<!-- todo-add link to async communication -->

I will go deep into messaging queues in the future blog post about **_sync and async communication._**

## Microservices: Monolith To Microservice Decomposition Patterns

It is not possible to break the monolith down into Microservices in a day, its a complex process which takes alot of thinking and planning on engineers part, but as the time has went by and more and more big companies like netflix , uber and aws have adopted microservices, they have comeup with patterns of decomposition which are now battle tested and work really well, so engineers need to know those patterns so they don't reinvent the wheel, following i will list down some patterns and i will discuss some of them in the future blog posts.

<!-- todo-add links to the following -->

- strangler fig pattern
- domain driven design
- Event driven decomposition
- database splitting
- backend for frontend

## Microservices: Advantages

### Flexibility With Technology Choice

This is one of the best pro of having microservices architecture, you can choose to develop every service with different tech stack, for instance you can use a database for a service which is suitable for the service workload and now you are noot shackled by the monolith, e.g if the workloads for the service are write heavy, you can use a DBMS which uses LSM tree as an internal datastructure but if the workload is read heavy you can use B-Tree based DBMS, same story repeats for the columnar or row based databases.

In the same way you can choose the implementation language for the service on different criterias, Golang if you want good out of the box concurrency and python if good library support is needed and concurrency is not much of a concern.

### Robustness

Microservices are more robust compared to monoliths, if monolith application fails the system fails, in case of microservices a single service could fail for some time but the whole system does not come down crumbling, if the failure is not cascading.

### Scaling

Just like microservices are invidually deployable, they are also individually scaleable, and thats a big deal, in case of monoliths you need to scale the whole application even if small part of the application needed scaling

Imagine an e-commerce platform with the following functionalities:

- User Service: Handles user authentication and profile management.
- Order Service: Manages order placement and history.
- Inventory Service: Tracks product stock.
- Recommendation Service: Provides personalized product recommendations.
  In a monolithic architecture, all these services are part of a single application. If product recommendations experience a spike in demand (e.g., during a sale), the entire monolith must scale, even though only the Recommendation Service requires more resources.

In a microservices architecture, these functionalities are separate services. Each can be independently scaled based on demand:

During a sale:

- Recommendation Service: Scales to handle the increased load, spinning up additional instances.
- User Service, Order Service, and Inventory Service: Remain unaffected and do not incur extra costs.

### Ease Of Deployment

Imagine changin one line in million line monolith application and redeploying, thats too much risk and apart from that you would also need to run all the tests hoping that the change has broken nothing.

Now on the other hand microservices are small code bases and changes and deployments both are fast and even if anything breaks its easy to trace and fix.

> you can comeup with alot more advantages of microservices but these are the main advantages which entice mojority engineering teams to invest time and effort to migrate to microservices architecture

## Microservices: Disadvantages

### Developer Experience

One of the main pain points of developing a microservices architecture is terrible developer experience, because it is very difficult to mimic the runtime environment locally, your single computer can only run fraction of total microservices. and if you are developing in the cloud thats even worse because the feedback cycles are longer.

### Technology Overload

There are like 50 solutions for the same problem in the market and every engineering team is using different tools to solve the same problem in different companies,so the engineers have to keep up with the tool evolution ever so often, which is very demanding for example there are 20 tools to create a **_service mesh_** which i can name off the cuff right now, but all of them are doing the same thing (there internal implementation details could differ).

secondly the advantage of flexibility with the technology could turn into a disadvantage quickly, because you have so many technology choices to solve a problem that you can suffer with **_choice paralysis_** and that could consequently postpone the decisions.

and lastly you can gradually keep on adding new tools to the project like you start with docker swarm and then gradually shift to kubernetes and the same way you keep on adding tools, and when there is new onboarding on the team the new person would be terrified watching so many unnecessary tech being used, and that could effect the team productivity.

### Monitoring And Debugging

In monoliths there is single application running on the single server and that makes it very easy to monitor it and debugging the problems, because the **life of a query** is so simple, the client makes a request and the single server responds to the request, that is it.

But in case of microservices the life of the query gets exponentially bigger with the number of microservices communicating to full fill the query. consequently if there is some problem in the system its very difficult to pin point where the problem really is, and it is also very hard to monitor the whole system and get a **holistic** view of things, below the image is depicting a problem in microservices called **_Big ball of mud_**, it refers to the communication between different microservices as big ball of mud, and it sure is ball of mud,

![hello](/images/posts/microservices/partone/ballofmud.gif)

> now you can imagine why it is so hard to monitor and debug microservices

### Security

With a single-process monolithic system, much of our information flowed within
that process. Now, more information flows over networks between our services. This
can make our data more vulnerable to being observed in transit and also to poten‐
tially being manipulated as part of man-in-the-middle attacks. This means that you
might need to direct more care to protecting data in transit and to ensuring that your
microservice endpoints are protected so that only authorized parties are able to make
use of them.

### Latency

With a microservice architecture, processing that might previously have been done
locally on one processor can now end up being split across multiple separate micro‐
services. Information that previously flowed within only a single process now needs
to be serialized, transmitted, and deserialized over networks that you might be exer‐
cising more than ever before. All of this can result in worsening latency of your
system.

### Data Consistency

Shifting from a monolithic system, in which data is stored and managed in a single
database, to a much more distributed system, in which multiple processes manage
state in different databases, causes potential challenges with respect to consistency of
data. Whereas in the past you might have relied on database transactions to manage
state changes, you’ll need to understand that similar safety cannot easily be provided
in a distributed system. The use of distributed transactions in most cases proves to be
highly problematic in coordinating state changes

## Useful Links

- [Microservices at netflix](https://netflixtechblog.com/tagged/microservices)
- [DoorDash scaling microservices](https://careersatdoordash.com/blog/failure-mitigation-for-microservices-an-intro-to-aperture/?utm_source=blog.quastor.org&utm_medium=newsletter&utm_campaign=scaling-microservices-at-doordash)
- [Snap chat's journey from monolith to microservices](https://eng.snap.com/monolith-to-multicloud-microservices-snap-service-mesh?lang=en-US)

## Book Recommendation

Read the brilliant book by Sam Newman called building microservices[^4]

## References

[^1]: [what is service oriented architecture](https://aws.amazon.com/what-is/service-oriented-architecture/#:~:text=you%20implement%20microservices%3F-,What%20is%20service%2Doriented%20architecture%3F,other%20across%20platforms%20and%20languages.)
[^2]: [information distribution aspects of design methodology](https://www.researchgate.net/publication/221331713_Information_Distribution_Aspects_of_Design_Methodology)
[^3]: [Remote procedure calls](https://en.wikipedia.org/wiki/Remote_procedure_call)
[^4]: [Brilliant book: building microservices](https://www.amazon.com/Building-Microservices-Designing-Fine-Grained-Systems/dp/1492034029)
