Building a Java Microservices E-Commerce App


I’ll create a simplified store based on microservice architecture. This is going to be a headless backend application without a frontend. However, testing is possible with any REST APIclient, e.g., by using CURL or POSTMAN. In order to prepare for deployment, we need to make several important choices.

Domain-Driven Design
The first and foremost task is to find the core, supporting, and generic subdomains of the application. In DDD terms, it is called Strategic Design.
In practice, the IT and the domain experts (business) should work together to find the domains and the bounded context of the domains. If we think about the business model, then the bounded context is the model boundary inside which the same ubiquitous language is used.

Kindly see the image named AppdomainModelling for the diagram

As we can see, ordering products is the core domain for an e-commerce application. Also, there are some supporting subdomains (Product, Customer, Cart, and Delivery) mandatory for an online store. Usually, the core domain and supporting subdomains are developed internally.
Some generic, cross-cutting services are also essential for an e-commerce store like Payment, Recommendation Engine, Authentication, and Authorization (RBAC, ACL). They are called the generic subdomain and usually outsourced as there are many COTS (commercial off-the-shelf) applications available for those generic purposes. Please note that large real-world online shops would include more supporting and generic subdomains.
For this demo, we will develop the core domain (Order) and one supporting subdomain (Customer).


Database per Microservice
When you are interested in a short-term gain (like boosting initial development velocity), a single database is a better choice. But for long-term benefits, what microservices is all about, I’ll use the database-per-microservice approach, where both the Product and Customer microservices will have separate databases. In this case study, we need an OLTP database supporting the ACID transnational guarantee; any SQL database or a handful of NoSQL ones may apply here. However, I will choose MongoDB, the most popular among NoSQLs and the most widely utilized document database. Since version 4.0, MongoDB also offers a cross-table ACID transactional guarantee with its WiredTiger storage engine. As described in detail here, managing transactions in microservices offers completely new sets of challenges. For our demo, we will accept the eventual consistency and won’t implement the distributed transaction.


Unified Tech Stack
I’ll take a JVM-based tech stack for its legendary backward compatibility and being the number one choice in enterprise software development. Stable and reliable Java™ will suit best as a programming language for our purposes. What about the runtime? Oracle has recently introduced a hefty price for Oracle’s JDK use in production. Fortunately, several companies are offering OpenJDK (which is completely free) with commercial licensing. BellSoft Liberica JDK is one of the industry’s leading OpenJDK distributions, 100% Java SE compatible, and supported by an active contributor to this project.
As for the backend, several excellent Java-based enterprise software development frameworks are prominent among the community. Spring from Pivotal, MicroProfile from the Eclipse Foundation, Quarkus from Red Hat, and Micronaut from Object Computing Inc. are the dominant ones in this field.
Among them, I will pick Spring since it has been the primary native Java-based enterprise software development framework for the last 20 years and is still going strong in the age of cloud computing. An additional advantage is that Liberica JDK is the default runtime in Spring, which makes our stack of choice uniform and strong.


Cloud-Native Infrastructure over Libraries/Frameworks
In the microservice architecture, we have to deal with common issues like service discovery, load balancers, scaling, and fault tolerance. Netflix OSS has some excellent libraries that would help to address those issues. But our microservices will then be tightly coupled with a specific programming language.
In the future, if we decide to migrate them to other languages, we will face issues. In the future posts, I’ll turn to infrastructure, like Docker and Kubernetes, to address this.
This part of the series only shows the source code and structure without any deployment.


Synchronous Communication
I will start with synchronous communication using REST because it is simple to implement and a popular way of communication.

Component View
Please see the 2nd image named EcommerceApp_component view

Here we are covering only the Customer and Order microservices. Microservice architecture follows the single responsibility principle and each service handles only one specific task.
In this demo, the Customer microservice will deal with every customer-related action: Create, Update, Delete, and Feed customers.It will do much more in a real e-commerce shop (like activate/deactivate customers, onboarding, etc.). Our Customer microservice will only do the basic things for the sake of simplicity.
Similarly, the Order microservice will manage everything regarding an order: Create, Read, Update and Delete orders. As I’ve already mentioned, they will have a separate data store (database/table/collection).
The frontend (web, desktop, and mobile) or the REST clients will connect with these microservices via REST API, i.e., REST API will serve as the contact between the frontend and the backend.The Order microservice in our example needs to communicate with the Customer one. When a customer orders via the Order frontend, an Order is created in the Order database. Then it uses the “customerOrders” API to link the Order with the Customer. The Customer microservice will then maintain the link between the Customer and Order.

Implementation
This use case will employ API-driven development, meaning we will first define the microservices API. It will then enable individual teams to work on the implementation without waiting for others. We’re going to apply the Swagger 2.0 REST API specification.

System requirements:
JDK 11;
Gradle;
MongoDB (Local or Atlas cluster);
Docker;
Kubernetes (kubectl CLI);
An AWS Account;
AWS CLI Version 2;
Eksctl CLI.
