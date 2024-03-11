# System Design Fundamentals

**Reliability:**

     ** The system should continue to work at desired level of performance even in the face of adversity ( both H/w and S/W faults { Things that can go 
        wrong})
     ** Systems that can anticipate faults and cope up are known as Resilient systems

**Scalability:**

**Maintainability:** 

    ** 3 aspects of Maintainability
         1) Operability : Make it easy for operations
         2) Simplicity: Make it easy for new engineers to onboard and understand
         3) Evolvability: Make it easy for code changes i.e. modifiability , plasticity and extensibility

**Elastic:**

    ** Some systems are Elastic meaning that they can automatically add computing resources when they detect a load, where as other systems are scaled 
       manually

**Head of line blocking**

    ** A server can only process a small number of requests in parallel ( limited for ex by CPU Cores). It only takes a small number of slow requests to 
       hold up the processing of subsequent requests 
       
**Http vs Http(s)**

**What is a socket ? what is a connection ?**

**How does DNS work:**

    ** The goal is to resolve a fully qualified domain name into a IP Address.  At each step , the corresponding components in this diagram look into 
      their cache first. For instance client first looks into browser cache and operating system cache. 
      Similarly DNS Recursive resolver also looks into its cache.
    ** Similarly Root name server and TLD server as well.
    ** DNS resolution can happen two ways: 
         1) Recursive and
         2) Iterative
    ** The Recursive query is between a DNS client and its local DNS server.Iterative query is  between a local DNS server and other DNS servers and Dont 
       demand a name resolution, which means other DNS servers may respond  a name resolution if they know or simply respond with a referral 
    ** The flow can be imagined with an example:
       Say I type www.google.com on my web browser. The browser will check two places first to see if there is any previous name resolution record of 
       this google machineone place is computer's cache memory . On windows it is ipconfig/displaydns and other place is a simple text file called Hosts
       Suppose the record doesn't exists. Then the DNS client queries the local DNS Server which is a recursive query,to resolve a name is the       
       responsibility of my local DNS Server 

![Screen Shot 2022-04-28 at 7 35 10 AM](https://user-images.githubusercontent.com/7702406/165776998-f824b2bf-7b7e-4444-84ba-44ff55dd358e.png)


_**How does a Load balance work:**_

What is a load balancer?
Load balancer is a REVERSE PROXY maintaining / managing a pool of application servers.

What is a REVERSE PROXY?
Load balancer is called Reverse Proxy because it acts as a proxy server between the client and the application server.

Similarly a proxy for Client is known as a Forward Proxy. 

Key terms:![Screen Shot 2022-05-03 at 4 13 41 PM](https://user-images.githubusercontent.com/7702406/166587870-e4a84963-3f0d-4844-b5b0-201a4613ba79.png)

1) VIP — Virtual IP
2) VS — Virtual Servers
3) RS — Real Servers

A VIP is hosted on the load balancer to represent the application. So the mapping is such that the VIP is mapped to the real servers and these are mapped to the application(s).

LB uses a connection table which has 5 fields:
a) source IP address
b) Destination IP Address , which is the address of the VIP
c) source port , is a number between 1–65535
d) destination port , again is a number between 1–65535
e) IP protocol either TCP or UDP or POP3 or any of the known protocols

Traffic is distributed to the Real servers using any of the following distribution algorithms
a) Round robin
b) Weighted Round robin
c) Least connection — This is the recommended approach
d) Weighted least connection
e) Agent based adaptive balancing
f) Fixed Weight
g) Weighted response time
i) Source IP #

LB constantly checks for the health of the real servers. In Production grade environments this is of the order of 5 to 30s . That is every 5s the LB checks for the health of the real servers.

How does the LB know the health or what are the internal workings?
A Load balancer can check the health across any of the layers of the OSI Model i.e. from Layer2 to Layer 7

1) Layer 2/3: LB uses the ARP ( address resolution protocol ) and ICMP ( i.e, ping the real server and if understand if there’s a response or not )
2) Layer 4 :This is at the Transport layer . The LB uses either TCP or UDP to know the status of the Real server. Note that even using this , all LB will know is about the status of the Real server but not of the application. For TCP the LB uses the 3 way handshake i.e. by sending Sync , Ack and close if it receives Sync Ack from the server. UDP is slightly different. It’s a connection less protocol. The LB uses a packet or a datagram and sends it to the server. The server may or may not respond however if the server is not listening then it returns a UDP data gram unreachable error.This takes us to the next step which is what if the LB wants to know the health of the application and not just the real server ? LB in this case uses a
3) Layer 7 check: LB will send a standard GET request and if it gets a 200 OK , it means application is up and running. The GET could request for a custom health check data as well . Layer 7 we can inspect the headers, since we are operating at the Transport layer , check incoming request and then check for the application status or either use it for Content switching . For instance the LB can define rules to distribute loads of requests like *.jpg to specific server and similarly requests for *.php to another set of servers.

![Screen Shot 2022-04-28 at 7 47 29 AM](https://user-images.githubusercontent.com/7702406/165779749-8caa9276-d213-4b29-82ae-e36d1e48388c.png)

This property is also used in GSLB ( Global server load balancing). 

What is GSLB?

GSLB is not a reverse proxy. It doesn’t proxy an application . This operates at the Geography level and Data center level to enable High Availability , low latency and Disaster recovery. GSLB provides application availability when application resides in multiple sides.GSLB servers have configurations that route traffics to a backup Data center when the primary Data center is down.Alternatively GSLB can also be configured to route user requests to nearest Data center and is a good for low latency applications. Examples could be routing all user requests originating from North America region to be routed to the Data center in US and routing all user requests originating in Eurpore to be routed to the Data center in Europe.


![Screen Shot 2022-04-28 at 7 47 11 AM](https://user-images.githubusercontent.com/7702406/165779656-e7fd442e-2c28-47d8-8769-fc6c7d31fbc5.png)

Some of the commercially available Load balancers:
F5 and Citrix

Load balancing algorithms

1) Round robin
2) WRR
3) Least connections
4) Least Response time
5) IP Hash 
6) Consistent hashing 

**What are Proxies? What is a Forward Proxy and a Reverse Proxy?**

![Screen Shot 2022-04-28 at 3 16 57 PM](https://user-images.githubusercontent.com/7702406/165856779-8557e556-915e-497e-96a5-9e0c84d2cd20.png)
 
Forward Proxy - Used to proxy Clients i.e. Server doesn't know who is the

Uses and Benefits:

1) Anonymity
2) Caching
3) Blocking unwanted sites
4) GeoFencing - For instance Redirecting users from a certain location to access only servers from a certain locations

Reverse Proxy - Used to proxy Servers i.e. Client doesn't know who is the server.

Uses: 
1) DDOS 
2) Firwall
3) Block bots and hackers
4) Load balancing
5) In the context of microservices --> Side car Proxy or Http Proxy or TCP Proxy 
6) Canary deployments - Set a rule in reverse proxy for experiments For ex: say 8% of the traffic to test a feature

Service Mesh - Proxy and Reverse Proxy are used at the same time based of side car proxy like Envoy or Istio

There are other type of Proxies also which are the **TLS Termination Proxies** , which can decrypt the encrypted data sent through them.


One of the most common use cases of Reverse Proxy is a Load balancer.

Forward or Reverse proxy is determined by the direction in which data is being sent

Advantages:

1) Proxies can be use to selectively send and block requests
2) Log or Monitor requests
3) Cache responses

There are two broad categories of Proxies:
1) Transparent proxy: Only looks at Layer 3 and Layer 4 and if the client is allowed to access the server , the request is only then forwarded
   This type of proxy is used only for filtering requests
   
2) HTTP insecure proxy: This is used in Service Meshes. TCP Packet is routed to proxy which then looks at the data to know the actual direction. Server may know the client if host is added as a Layer 7 header. Http1.0 did not support host as the header. Http2.0 supports host as a header.

**What is CDN and how does it work?**

CDN stands for Content Distribution Network
This is modern and popular solution for minimizing request latency when fetching static assets from a server. These are composed of group of servers that spread out globally such that no matter how far a user is from origin server , they will always be close to a CDN Server.

Instead of fetching static assets from origin server, users can fetch cached copies of files from CDN more quickly

Some of the popular CDNs:

1) Cloudflare CDN
2) AWS Cloud Front
3) GCP Cloud CDN
4) Azure CDN
5) Oracle CDN

Every CDN has its own local cache and they should all be in sync. There are two ways to populate a CDN Cache

1) Push - It would be the responsibility of Engineering to push new assets to CDN
![Screen Shot 2022-05-03 at 4 09 52 PM](https://user-images.githubusercontent.com/7702406/166587505-73dc29fc-da75-4549-9a9c-f4b08f427839.png)
3) Pull - Server cache is lazily updated. First hit is always a miss.
![Screen Shot 2022-05-03 at 4 14 10 PM](https://user-images.githubusercontent.com/7702406/166587902-88fe7faf-c15d-4043-8a1d-ad732a21692d.png)
A Pull method is usually recommended. However there are few challenges that we run into with Pull CDN.


1) Stale assets can be returned as CDN doesn't know  if an asset is updated


So to mitigate this :

1) Associate a time stamp to the asset
2) or use Cache-Control headers. So browser based on these header MAX-AGE{Amount of time it takes for a cache copy to expire} sends another request to the server
3) Another approach is Cache busting. You cache assets with a hash or e-tag {is unique compared to previous versions}

**Cache**

   ** When to use Cache ?
       ** Data is read frequently but modified infrequently
       ** Expiration time or TTL should not be too high nor too low
      
   ** What is Cache busting?
      ** This is a mechanism to tell the browser that new version of the file is available. So browser doesn't retrieve the old file from Cache but 
         rather makes request to origin server for new file. The advantage of this is that users receive the most recently updated file without having to 
         perform a hard refresh or clear browser cache
   ** What are Cache eviction poilicies ?
      ** LRU
      ** LFU
      ** FIFO

   ** What are the caching strategies?
      ** Read Strategies
         ** cache Aside (Lazy Loading)
            ** Tries cache first and then fetches from DB on Cache miss , when cache 
               misses are rare or latency of a cache miss + DB read is acceptable
         ** Read Through
            ** Cache handles DB Reads , transparently fetching missing data on cache miss. This abstracts DB logic 
               from app code and keeps cache consistenly populated by handling misses automatically
            
      ** Write Strategies
There are three ways to implement Cache busting:

1) File name versioning
2) File Path versioning
3) Query strings 

Option 3 Above is not recommeneded because some proxies & CDNs are not able to cache

The recommended ways are **File name versioning and File Path versioning.**

Dyanamic Content Caching: The idea behind Dynamic content caching is that not all info on the page is same for all users. Some elements are personalized either based on Geography i.e. location , time in day , device types etc.

Dynamic content is generated by scripts that change the content of the page by running scripts in CDN Cache.

Scripts can be run on the CDN cache such that Dynamic content is generated and served to clients or static content be altered , cached or purged from Cache


**API Gateway** 
Traditional architectures relied on clients making direct calls to the BE APIs. The disadvantage of this is a force upgrade of the client which is not a desirable experience. There a two ways to mitigate this 1) BFF {backend for front end }, similar to API Gateway or an extension to API Gateway and 2) a plain API Gateway

![Screen Shot 2022-05-03 at 4 31 56 PM](https://user-images.githubusercontent.com/7702406/166590748-e9ef4f72-176b-4979-b7e0-4ff934019ed2.png)

From the image above we can see the advantage that  with BFF say for a Mobile we can have a configuration setup done in API Gateway for faster and better UX experience. An example could be that for requests originating from Mobiles and routed through Mobile API Gateways the BE response is light weight.

Applications of API Gateway range from 

1) Authentication and Authorization via JWT. Instead of each microservice authenticating and authorizng a request , the API gateway can authenticate and authroize the incoming request and inject a JWT token allowing services to strictly focus on the business
2) SSL Termination for firewire communication. Http(S) authentication is slow , so API gateway can authenticate the incoming request and then terminate the SSL so all subsequent calls would be http , and so are faster
3) Load balancing: API Gateway can route the request to a micro service or an instance of microservice
4) Insulation :  API Gateway can act as a firewall and limit the number of calls and thus prevent a DDOS {Distributed denial of service}
5) Rate Limit: To prevent DDOS
       Ways to rate limit:
       1) user based : Which user is allowed
       2) Concurrent: How many parallel calls are allowed , helps you mitigate DDOS
       3) Location / IP  Useful to run campaigns / restric service to a location / IP
       4) Server based rate limiting:
                1) Token bucket
                2) Leaking bucket
                3) Fixed Window counter
                4) Sliding window log
                5) Sliding window counter { combination of sliding window log + Fixed window counter}
7) Handle API Versioning
8) Error handling
9) Caching


Considerations before using API Gateway:

1) SPOF: Since API Gateway can be a single point of failure , one way to mitigate this potential issue is by having multiple API Gateways
2) Scaling:  There would be aneed to  scale the API Gateway as the scale of the app grows
3) Routing complexity: All the routing resides now in Gateway . Not necessarily a complicated one but def complex

Some of the COTS :

1) APIGEE
2) NGINX - Solves the C10K problem i.e. concurrently handling 10K connections


The advantages of API Gateway:

1) Client code need not change
2) No forced upgrades of client

The disadvantages could be :

1) Maintanence and 
2) API Gateway could be a SPOF
3) Increase the latency as this will become an additional hop between client and the back end services


**What is Consistent Hashing?
What is consistent hashing with Vnodes?**

In Data paritioning how do we know which node has a particular piece of  data or otherwise which node should be store the Data ?

A basic approach will be to use a suitable hash function to map the key of the data to a number. Then find the server by applying modulo on this number and the total number of servers

Ex: say the key to the Data is "California"
say we compute hash(key) = hash (California) = 16
Now 16%{Number of servers} will give the server ID on to which we must store the data.
Extending the example say we have 5 servers , 16%5 = 1 , that means the Key California should be stored on Server with ID 1.

The disadvantage of this approach is that 

a) Adding and removing servers means all our existing mappings will be broken and so we have to remap all the keys each time servers are added or removed

So the way to solve this is Consistent Hashing

Consistent hashing is the technique to distribute requests/ data efficiently across servers.

Real world systems using Consistent Hashing:
1) Discord chat application
2) Akamai CDN
3) Data partitioning across the clusters in Apache cassandra
4) Partitioning component of Amazon's Dynamo DB

With Consistent Hashing only a small set of keys move when the servers are added or removed.

Consistent hashing stores the data managed by a distributed system in a ring. Each node in the ring is assigned a range of Data ( token ).

This schema works well when a node is added or removed. We just have to rebalance data on the node that is removed and move that to the next node , while adding node we have to rebalance the range.

However this scheme can lead to non uniform distribution of the keys and so thus can be solved using an extension named Virtual nodes

![Screen Shot 2022-05-17 at 10 19 04 AM](https://user-images.githubusercontent.com/7702406/168872912-326966af-8fe9-4c80-aa25-0605adfb8d42.png)

**What is Consistent Hashing with Virtual nodes?**

Instead of assigning a single token to a node the hash range is divided into multiple smaller ranges and each Physical node is given several of these ranges

<img width="843" alt="Screen Shot 2022-05-17 at 10 39 21 AM" src="https://user-images.githubusercontent.com/7702406/168876133-0edee5fe-c077-474c-95bf-5ebc10a2205d.png">

Example: Consider the above distribution where we 

Server 1 :With range 0-25  
Server 2: With range 26-5  
Server 3: With Range 51-75  
Server 4: With Range 76-100  

In a Consistent Hashing with Virtual nodes the each Physical node  will get a smaller range as in the image below

<img width="843" alt="Screen Shot 2022-05-17 at 10 43 50 AM" src="https://user-images.githubusercontent.com/7702406/168876897-ae2382b9-4564-45f4-8d5e-d311fd547ddd.png">

Practically , Vnodes are randomly distributed across the cluster and are generally non-contiguous so that no two neighboring Vnodes are assigned to the same physical node or rack

Additionally for data replication , each node contains replicas of data from other nodes. 


**Microservices and patterns.**

Ways to break Monolith into Microservices 

1) Functional decomposition i.e. Decomposition based on functional areas

Advantages of decomposition :

1) We get the ability to use the right tool for the right process and support polyglot persistance
2) Easy to scale
3) Easy to deploy
4) loose coupling - changing one part of the code doesn't affect others
5) Faster to develope and understand
6) High Availability

Disadvantages:
1) Inter process communication
2) Distributed transactions ( 2 phase commit or SAGA)
3) More resources
4) Debugging issues

How to scale microservices?
Its called SCALE CUBE.  

The 3 dimensions are:
1) Functional decomposition
2) Horizontal scaling
3) Data partitioning

Vertical scaling can also be an option however there is a hard end to the computing power and cost rise is exponential

What role does API Gateway play in Microservices?

Without introducing Gateway will lead to change in client code which is not desirable. Refer to section API Gateway

Advantages:

1) Authentication
2) SSL Termination i.e. Converting Https calls to Http for faster communication. Https can be at gateway layer & all internal service calls can be Http. Thus SSL can be terminated
3) LB
4) Insulation

API Gateway can also be used for:

1) Caching
2) Managing access quotas
3) API Health monitoring
4) API Versioning
5) Chaos moneky testing
6) A/B Testing i.e.  Traffic split Ex: 70% to one deployment and 30% to another deployment

**Service Discovery: How does the API Gateway redirect?**
So the question is how does one microservice connect to another microservice or how does API Gateway redirect requests from client to any instance of a microservice

The pattern used to solve this problem  is called **Service Discovery** which relies on **Service Registry**. The Service Registry maintains a list of all microservices and their Network addresses

Ex: Eureka 

**Service Registry** : Its a store containing a list of services , ip addresses with ports

How does Service registry get latest address of any service?

1) Self Registry - Any new service or service instnaces registers to Service Registry
2) 3rd Party - Service registry makes a call to the 3rd party and asks for new services or new instances for existing services. Service registry listens to new services on cluster and asks for the network addresses and saves the addresses

//TODO Add a diagram and dig deep on Service Registry

How ever there is a disadvantage as well when a client makes a call to Service registry directly , the client will have to Load balance between the multiple instances of a service as returned by Service Registry so we move to **Server side discovery** . Look at the below diagram

![Screen Shot 2022-05-25 at 5 56 04 AM](https://user-images.githubusercontent.com/7702406/170267006-bc37430b-0065-4009-8390-926b261fb2e9.png)

**Inter service communication**
1) Http or RPC ( remote procedure calls)
2) Synchronous or Asynchronous calls

|  Synchronous         | Advantages       | Disdvantages  
--------------------| -------------  | ------------       
|1| Easy to implement |Service Availability can lead to break of functionality
|2| Real time  responses | Response times are dependent on dependent services
                     
         
|  ASynchronous         | Advantages       | Disdvantages  
--------------------| -------------  | ------------       
|1| Faster APIs | Complex Design
|2| Decoupled        | Adds to process latency
|3|No need for service discovery | Monitoring costs 
|4|Works even when services are down | Error reproducibility is hard

**Ciruit breaker**
I've used Hystrix as ciruit breaker in my projects

Code snippet:
@Hystrix Command
(fallback Method = "<name of the fallback method in case of error >" , commandKey= "< name of the method that can fail>")
 
**Features supported by circuit breaker:**
1) Returns a cache response before the service was running
2) Redirect to another call that is a fall back service in case of error. This is called Fall back mechanism
3) Lets the service under failure hearl. For subsequent requests the circuit breaker directly calls the fall back instead of calling the service that is failing
 
**State diagram of circuit breaker:**
 ![Screen Shot 2022-05-25 at 6 21 30 AM](https://user-images.githubusercontent.com/7702406/170272019-63a8cf04-b6fa-4318-a436-b1869fb6f0cf.png)
 
****Service Mesh****
 What is Service Mesh(SM)? 
 
            *** SM is a dedicated infrastructure layer for making service to service communication safe , fast and reliable. The Serice mesh is a network 
                of side car proxies that are deployed alongside individual application containers. The sidecar proxies intercept all network traffic and 
                implement various platform requirements pertaining to service-to-service communication so that application does not need to implement 
                functionalities that all cloud native applications require
 
 Why do we need Service Mesh. Let's look at how microservices would interact without a Service Mesh
 
 ![Screen Shot 2022-05-25 at 7 09 27 AM](https://user-images.githubusercontent.com/7702406/170282429-f8286703-6d4c-420d-a2e7-062db85f5b73.png)
 
 If Service 1 is calling 2 and since there are multiple instances of 2 , a lookup in SR happens and then 1 needs to loadbalance and so basically this is beyond the boundaries of reponsibility for 1. Also stuff like retry & circuit breaker logic doesn't necessarily are responsibilities of 1 .
 2) Similary 2 is calling 3 and 4 so it has to repeat the circuit breaker logic and retry logic 
 3) So the challenge is that 1 and 2 have to handled within every microservice
 4) The solution is to have a **Service Mesh**

 Service mesh is a component that runs with every microservice. For every instance of microservice service mesh runs in parallel and the service mesh runs for any of the microservices that are live. The Service Mesh also has metrics server to deal with Service metrics. With Service mesh the caller avoids service discovery and loadbalancing 
 
 A Service mesh has 2 components:
 
 1) Control plane - This is where we configure proxies that are side loaded with every microservice
 2) Data plane - Has a group of proxies that don't have knowledge of other proxies in the cluster. All requests go through proxies in Data plane
 
 This pattern is called as the **Side Car Pattern**
 
 ISTIO is a Service Mesh 
 
 **Microservices deployment**
 
 Goals of deployment:
 
 1) Scalability and throughput
 2) Reliable and available
 3) Isolation ( one service doesn't pull the entire system down)
 4) Monitoring
 5) Cost Effective
 
 Way to deploy:
 
 1) Multiple services per host 
           a) this is a very traditional approach
           b) The disadvantage is that we share resources
 2) Service per vm per container : This is a widely adoped technique
           a) Isolation and security
           b) FAST
           c) Autoscaling can achieved through  Kubernetes or Dockerswarm container orchestration techniques
 3) Serverless - No need to worry about Service Registry , API Gateway . We just develope code 
            ex: Aws Lambda , Google cloud function.
    We just get into console and paste your code , the API will be up and running
    There are both advantages and disadvantages to this approach.
    
  Advantages of using Lambda or Cloud functions:
    1) Focus only on code and business
    2) No worries about scaling
    3) Pay as you go
 
  Disadvantages:
 
    1) Run time support , not all run time environments are available
    2) Expensive
    3) Vendor lockin 
    4) Debugging
    5) Stateless and short running process only
 
**Distributed transactions in Microservices:**
 
 1) 2PC ( 2PHASE commit) : 2 Phases are a) Prepare and b) Commit
 This leads to a strongly consistent model however is synchronous in nateure and increases latency
 2) 3 PC ( 3 Phase commit) - Similar to 2 Phase but has another additional phase called pre-commit
 3) TC/C 
 4) Saga
        a) Choreography - Event based , async communication , every transaction has a compensating transaction to roll back
        b) Orchestration - When we are building process flow around existing services

 SAGA is useful to handle business errors like credit card violation but cannot be used to handle technical errors based on the non-deterministic nature 
 of the system. Good way is to implement a technical layer that guarantees that updates sent to it will be eventually complete. This is done by SAGA 
 execution coordinator
 
 SAGA Choreography pattern :  There are no http calls at all.
 Every operation that is part of the SAGA can be rolled back by a compensating action.
 Transactions in microservices are BASE ( Basically available soft state and eventually consistent ). Compensating actions must be taken to revert   
 anything that occured as part of the transaction. The following are the frameworks to implement SAGA Choreography
 
 As we can see in the below picture there are no http calls in the SAGA choreography flow
![Screen Shot 2022-05-25 at 2 46 42 PM](https://user-images.githubusercontent.com/7702406/170372753-3dab1572-343c-460c-8e60-ba79fdd0860e.png)

 1) Eclipse Microprofile LRA
 2) Eventual Tram Saga
 3) AXON Saga  {Spring boot + Axon is a popular choice}
 4) Seata ( this is used by alibaba)
 
 SAGA Execution Coordinator (SEC):
 This is a core component for implementing a successful SAGA flow. Maintains a SAGA log that contains a sequence of events of a particular flow
 This component maintains a SAGA log that contains sequence of events of a particular flow. IF a failure occures the SEC queries the logs and helps identify which components are impacted and in which sequence the compensating transactions must be executed. SEC Helps maintain a **eventually consistent state**
![Screen Shot 2022-05-25 at 2 56 40 PM](https://user-images.githubusercontent.com/7702406/170374017-d7296519-a972-4182-9211-86606c5bcf50.png)

 If SEC itself fails it can read logs to identifyw hich of the components are succesfully rolledback , identify which ones are pending and start calling them in reverse chronological order to roll back
 
 SEC can be a standalone component ( in most scenarios it is ) or a part of existing microservice
 
 **SAGA Orchestration pattern:**

 This is ideal for scenarios where you 've already built your microservices and now want to create a process flow.
 There would be a orchestrator to manage the entire operation from one cdenter. An orchestrator receives a start command from source and commences calling  
 related services sequentially.  After each succesful response it makes the next call to another service. If one of the steps fails and service returns a 
 failure message , the orchestrator makes roll back calls for previous step
 
 Some of the popular frameworks:
 1) Camunda
 2) Apache camel : I've used this in my projects. Basically a series of actions in camel route that should be either completed successfully or not 
    executed and compensated
 3) IBM App coonnect
 
SAGA ORCHESTRATOR VS CHOREOGRAPHY:
 If we are building new products and building new services , use Choreography for event based transaction.
 If we are creating a process around existing set of miscroservices use Orchestrator
 
 **Database concepts:**
 
Data model types:

At a higher level there are two broad categories: a) Relation Databases b) Non relational databases

1) Relational model - The query optimizer automatically decides which parts of the query to execute and in which order , along with indexes to use. 
   If we want to query data in new ways , you can declare a new index and queries will automatically use the most appropriate indexes
   
   For relational models , schema changes have a bad reputation of being slow and requiring down time.
   Relationships are handled  through Foreign keys.
   
   For highly interconnected data , the relational models are ok
   
   MySQL copies the entire table on Alter command which can mean minutes or hours of down time.
   
   Similarly running update statement on a large table is likely to be slow on any database since every row that matches criterion needs to be rewritten.
   
2) Document model - Support for joins is often weak. DB doesn't support a join. Relationships are handled through Document references. Use this model if data in the application has document like structure. 

For highly interconnected data the document model is awkward.

If you're application has mostly one-to-many relationships or no relationships between records , the document model is appropriate? //TODO Why

3) Graph model

For highly interconnected data graphy models are most natural

Graph has two objects: Vertices and Edges

Graphs are not limited to homogeneous data. This provides a consistent way of storing completely diff types of objects in a single data store. Ex: Facebook

There are two types of Graph models:
a) Property graph models - Cypher is a declarative query language for property graphs created for Neo4j
b) Triple store model - SPARQL , query language for triple stores using RDF data model

 
4) Column oriented storage
 
    a) Idea is that don't store all the values from one row together but store all the values from each column together
    b) The way it works is that to reassemble entire row , you can take the entry from each of the individual column files and put them together to form  
       the row in a table
    c) Another advantage of column oriented  storage is that data can be compressed. Common compression is bit map encoding.

**Non Relational databases can be used if:**

    a) Application requires super low latency
    b) Data is unstructured
    c) Need to serialize and de-serialize data
    d) store massive amount of data
    
In-Memory db: The performance gain of in-memory db is not due to the fact that data is not read from disk but because they can avoid the over heads of encoding in memory data structures in a form that can be writtern to disk

Graphs are good for evolvability as we add features to application . A graph can be extended easily to accomodate changes in applications data structure.

Schema on read : Structure of data is implicit and interepreted when data is read . This is similar to dynamic ( run time) checking in programming language

Schema on write: The traditional approach of relational databases where the schema is explicit and the database ensures all written data connects to it. This is similar to static (compile time )type checking

What is impedence mismatch? 
Disconnect between the models i.e application code and database model of tables rows and columns

What is Normalization?
Removing duplication is the key idea behind normalization
 
1) _Polyglot persistence_: Relational daabases will continue to be used alongside a broad variety of non-relational data stores and this is known as  
2) _Query optimizer_: In a relation database the query optimizer automatically decides which parts of teh query to execute in which order and which   
    indices  to use.
 
 If you want to query data in new ways you can declare a new index and queries will automatically use which ever indexes are most appropriate .
 
 //TODO Try creating a new index and how Db Engine handles it
 
 How does indexing work?
 
In simple terminology, an index maps search keys to corresponding data on disk by using different in-memory & on-disk data structures. Index is used to quicken the search by reducing the number of records to search for.
 
 How to determine the db engine?
 
 SHOW TABLE STATUS WHERE name = '<table name>' \G;
 
 show index from <table_name>; -- lists all indices in a given table
 
 EXPLAIN SELECT * FROM  <table_name> WHERE <column_name>='<some_guid>'; -- Explains the way query is executed
 
 
 EXPLAIN shows how the query engine plans to execute the query. 
 
 The screen shot below shows all the columns that are returned

 ![Screen Shot 2022-06-28 at 11 16 51 AM](https://user-images.githubusercontent.com/7702406/176254087-eb98d9b6-d53d-4320-ab92-c5d1b8e297ae.png)
 
 possible_keys: represents what all available indices are there which can be used in this query
 key: represents which index is actually going to be used out of all possible indices in this query.
 rows: column indicates the number of rows MySQL believes it must examine to execute the query. For InnoDB tables, this number is an estimate, and may  
 not always be exact.
 filtered: indicates an estimated percentage of table rows that are filtered by the table condition.
 
 WAL Vs SSTables VS LSM{Log structured merged} Trees:
 
 SSTables ( Sorted String Tables):
 ** Key value pairs is sorted by Key
 ** Each key only appears once within each merged segment file
 
 For Hash indexes , the data structure is Hash Map
 For SST - Red black trees or AVL Trees.  With these Tree data structures you can insert keys in any order and read them back in sorted order
 
 Cassandra and HBase use LSM out of SST. To avoid data loss in the event of a crash. Since the most recent writes to mem table are not yet written to disk are lost , we keep a separate log on disk to which every write is immediately appended.
 
 Lucene an indexing engine used by Elastic search and Solr uses a similar method. SST is used and merging happens in the back ground as needed
 
 LSM ( Log structured merge trees ) indexes are gaining acceptance. LSM Tree can support remarkably high write throughput.
 
 LSM can be slow when looking up for keys that donot exist in db. The db engines use a concept called Bloom Filters. Bloom filter is a memory efficient data structure for approximating the contents of a set. This can tell you if a key doesn't appear in the database and thus save many unnecessary disk reads.
 
 LSM Trees have low storage overhead especially when using levelled compaction
 
 Downside of LSM is that disks have limited resource, so it can easily happen that a request needs to wait while disk finishes an expensive compaction operation. Hence the response times of queries to LSM storage engines can be some times quite high and unpredictable.
 
 For LSM Engines if write throughput is high and compaction is not configured carefully , it can happen that compaction cannot keep pace with rate of incoming writes. In this case the number of unmerged segments on disk keep growing and they slow down the speed.
 
 **B-Trees:**
   
     ** B-Trees are most widely used indexing structure 
     ** This is a standard index implementation in almost all relational databases and many non-relational databases use them too
     ** Like SST Tables B-Trees keep key value pairs sorted by key which allowes efficient key value lookups and range queries
     ** B-Trees break the database down into fixed size blocks or pages. Traditionally 4KB in size and read or write one page at a time
     ** Each page can be identified using an address or location which allows one page to refer to another.
     ** Most databases can fit into a B-Tree that is 3 or 4 levels deep so you don't need to follow many page references to find the pages you are looking
     ** A 4 Level tree of 4KB pages with branching factor of 500 can store up to 256TB
     ** LSM Never modifies in place  but a B-Tree index updates are modified in place
     ** To make B-Trees support DB that is crash proof it is common for B-Tree implementations to include an additional data structure on disk : a write  
        ahead log  ( WAL). WAL is a append only file to which every B-Tree modification must be written before it can be applied to pages of the tree  
        itself. when database comes back after crash , this log is used to restore the B-Tree back to consistent state.
     ** For concurrency control latches ( light weight locks ) are applied
     ** B-Tree Optimizations: 
            ** Creating a copy on write schema
            ** Abbreviate keys
     ** B-Tree variants such as fractal trees borrow some log structured ideas to reduce disk seeks
   
   
   _**Rule of Thumb**: LSM Trees are typicallt faster for writes where as B-Trees are thought to be faster for reads_

**Secondary Index**
 
    ** Secondary index is created as _create index_.
    ** Several secondary indexes can be created
    ** In secondary indexes keys are not unique unlike Primary indexes where keys are unique
    **  Key in an index is the attribute that queries search for 
    ** Value can be a) an actual row or b) reference to row stored else where . The place where rows are stored is known as Heap file and it stores data          in no particular order
    ** When multiple secondary indexes are  present each index just references a location in the heap file and the actual data is kept in one place.
  
**Clustered Index**
 
    ** Stores all row data within index
    ** The PK of a table is always clustered index in MYSQL's Inno DB Storage Engine. 
    ** The secondary indexes refer to the Primary key
 
 **Covering Index**
 
    ** Compromise between a clustered index and a non clustered index.
    ** Stores some of the table's columns within the index. So indexes are to be created based on access and write pattern
 
 Clustered and Covering indexes can speed up reads but they require additional storage and can add overhead on write. Also DB needs to enforce transactional guarantees when we use clustered index or covering index
 
 **Concatenated Index**
 
    ** Most common type of multi column index
    ** Combines several fields into a key by appending one column to another
    ** Multi dimensional indexes are a more general way of querying several columns at one , which is important for geo-spatial data 
 
**Fuzzy search or Full Text Search indexes**
 
    ** Search for similar key OR
    ** Misspelled words OR 
    ** The secondary indexes refer to the Primary key
    ** Lucene is able to search text for word within a certain EDIT distance. EDIT distance of 1 means that one letter has been removed added or replaced 
 
 **Summary in choosing databases**
 
    1) Choose and understand the model
    2) Choose DB Engine i.e OLTP ( B Tree vs LSM vs SST vs In Memory)  vs OLAP 
 
 **OLAP**
 
    1) STAR Schema or Dimensional modelling. In this event is at the center
    2) Snow flake schema , Dimensions are further broken down into sub dimensions
    3) Snow flake schemas are more normalized than STAR Schemas. STAR schemas are more preferred because they are simpler for analysts to work
 
 **Materialized View**
 
    ** Snapshot
    ** A special case{A grid of aggregates grouped by different dimensions} is data cube or OLAP cube
    **  Most data warehouses use aggregates such as data cubes only as a performance boost for certain queries
 
 **Encoding and Evolution**
 
    ** Several formats for encoding data JSON,XML, Protocol buffers , Thrift and AVRO
    ** Translations of data in memory to sending it over Network is known as Encoding. Decoding is the reverse of encoding
    ** Various languages support libraries that come with built in support for encoding in-memory objects into byte sequence
    ** JSON , XML and CSV are  data interchange formats
    ** 
 
      **Binary Encoding**
        
        ** This is used for more compaction and to process data fast
        ** When this encoding is used on smaller data sets the gains are negligible but if the data size is in TB , the choice of data format will have a 
           big impact
        ** Message Pack , BSON , BJSON , UBJSON , BISON are some binary encoding schemes
        ** JSON is less verbose than XML but both still use a lot of space compared to binary formats.
        ** When encoding understand if the gains are big enough and worth the loss of human readability
        
      ** Protocol Buffers
      
        ** Encoding scheme originally developed at Google.
        ** Supports only one binary encoding format
        
      ** Thrift
        
        ** Encoding scheme originally developed at Facebook
        ** Has 2 different encodigs a) Binary Protocol b ) Compact Protocol
        
      ** Both Thrift and Protocol buffer encoding schemes need a schema for any data that is encoded  
      ** Both Thrift and Protocol buffer come with code generator tool that takes a schema definition and produces classes that implement the schema in 
         various programming languages. The application then calls the generated code to encode or decode

      **AVRO Encoding format**       
         ** When using AVRO the binary data can only be decoded if the code reading the data is using the same exact schema as the code that wrote the 
            data. Any mismatch in the schema between the reader and the writer would mean incorrectly decoded data
         ** Key idea with AVRO is that writer's schema and reader's schema don't have to be the same, they have to be compatible
         
      **gRPC
         ** The reason they are used is that custom RPC protocols with a binary encoding format can achieve better performance that something generic like 
            JSON via REST 
         ** The main focus of RPC Frameworks is on requests between services owned by same organization,typically within same datacenter
         
      **AKKA**
         ** Rather than dealing with threads, logic is encapsulated in actors.
         ** Is a programming model for concurrency in a single process
         
       **Summary**
         ** Text Encoding formats - JSON / XML / CSV
         ** Binary - AVRO , Thrift and Protocol Buffer
 
 
 **Replication**
   
    ** Replication strategies: ( Used if master goes offline , then one of the slaves is promoted ) 
       ** Multi master 
       ** Circular replication
    ** If slave goes offline  , reads are redirected to master temporarily until new slave is added
    
    ** Keeping copy of same data on multiple machines that are connected via network
    ** Keep data geographically close to users
    ** to serve more read queries
    ** Three popular algorithms to replicate data : Almost all the distributed databases use on the models below
       ** Single leader
       ** Multi leader
       ** Leaderless.
    ** Configurations in DB for replication
       ** Synchronous
          ** The leader waits until the follower has confirmed that it receieved the write before reporting the success to the user and before making the 
             writes are visible to other clients
          ** For Synchrnous the advantage is that the follower is guaranteed to have an up-to-date copy of data that is consistent with leader
          ** The disadvantage is that the leader must block all writes and wait until the synchronous replica is available again
          ** Typical configuration in Production is semi synchronous i.e. one of the followers is synchronous and others are asynchronous.
          **  In semi synchronous if a sync follower is slow async follower is made  synchronous
   
       ** Asynchronous
         ** The leader sends the message but doesn't wait for a response from the follower
         ** Replications is fast and under a second however if the following scenarios occur there could be a delay
            ** if Recovering from failure
            ** If the system is operating at capacity
            ** If there are N/w problems
       ** Handling failure replicas
   
    ** Leaderbased replication**   
       ** Also known as master slave , active passive 
       ** Every write to the database needs to be processed by every replica , otherwise the replicas would no longer contain the same data
       ** Some databases that support leader based replication
          ** Postgre SQL ( 9.0 and above) 
          ** MySQL
          ** Oracle Data Guard
          ** Mongo DB
       ** Leader based replication is not limited to Databases only , this scheme is used by message brokers like Kafka and RabbitMQ
       ** Leader based replication is configured to be completely asynchronous , if leader fails any writes that have not yet been replicated fails. 
          However the advantage is leader can continue processing writes . This is weak durability
       ** Weak durability may be a bad trade off however async replication is widely used
       ** To add a new follower and to scale , we take a consistent snapshot of the leaders db and then copy the snapshot to the new follower node. The 
          follower then connects to the leader and requests all data changes that have happened since the snapshot was taken
          
          Postgre use -- log sequence  & MYSQL uses bin log coordinates
          
       ** In case of leader failures 
            ** One of the followers needs to be promoted
            ** Clients need to be reconfigured to send writes to the new leader
            ** Other followers need to be reconfigured to send writes to new leader
            
       ** How to determine leader has failed
            ** Time out
            ** Choose a new leader through consensus
     
       ** WAL Replication
            ** Process the log build a copy of the exact same data structures as found on the leader
            ** Postgre SQL and Oracle use WAL &
            ** The main disadvantage is that this is to low level
            
       ** Logical (row-based ) replication
            ** Different log formats for replication and different for storage engine
            ** MySQL has this feature.
            ** Advantages :
                 ** Since a logical log is decoupled from storage engine internals , it can be more easily be kept backward compatible
                 ** Also easier for external applications to parse the log. Ex: send contents of db to data ware house for CDC {change data capture}

       ** Summary : 
           ** Basically replication is for high availability and being able to tolerate node failures is just one reason for wanting replication
           ** Another reason is scalability &
           ** to reduce LATENCY by placing replicas geographically close to the users
           ** Bascially leader based replication is a READ scaling architecture i.e. work loads consist of mostly reads  and only a small % of writes
           ** Replication lag due to asynchronous nature of replication - leads to eventual consistency {i.e. a temporary state where followers will 
              eventually catchup}
           ** Challenges with replication lag:
              ** Reading your own writes : with async replication user views the data shortly after making a write, if reads are coming from follower it 
                 looks like the data submitted is lost due to lag
              ** Read_After_Write : Read your writes , guaranteee that if user reloads the page they will always see any updates that they submitted , for 
                 other users updates may not be visible
            
       ** Implementing read-after-write in leader based replication
          ** When reading something that the users may have modified read it from leader, otherwise read it from follower
          ** what if there are a lot of things that user can change? 
             A) Cannot read from leader as it defeats the purpose of scaling so may be we monitor the replication lag  on followers and prevent queries on 
                any follower that is more than a minute behind the leader
          ** Use this in scenarios where users use cross devices ex fill form on mobile and log on to web       
       
   
![Screen Shot 2022-07-27 at 10 22 56 AM](https://user-images.githubusercontent.com/7702406/181310266-e52c3755-0bb7-45dd-a88c-26dedd5ebd54.png)

   
      ** Monotonic reads
      
         ** That if one user makes several reads in a sequence, they will not see time go backward i.e. they will not read older data after having 
            previously read new data
         ** One way of achieving monotonic reads is to make sure each user always makes reads from same replica. The replica can be choose based on the # 
            of the user ID , rather than choosing a random replica   
            
      ** consistent prefix reads 
      
      
      ** Transactions
      
         ** Transactions exist for application developers to not worry about subtle replication issues and could just trust their databases to the do the 
            right thing
         ** Single node transactions are OK , however in distributed ( partitioned and replicated ) databases may systems have abandoned these , 
            transactions are too expensive in terms of performance and availability
            
   **Multi leader replication**
     
     ** In leader based replication there is only one leader and all writes must go through it. If you cannot connect to leader you cannot write to the 
        database so a natural extension is multi leader replication Also known as MASTER-MASTER or ACTIVE-ACTIVE replication. 
     ** In this each leader acts as simulataenously as a follower to other leaders  
     ** In ACTIVE-ACTIVE / Multi Leader
        ** You can have a leader in each datacenter.
        ** Each data center's leader replicates its changes to leaders in other center
        ** Within each Data center in this setup a regular leader-follower replication is used
     ** Tools
        ** Tungsten replicator for MySQL
        ** Goldengate for Oracle
        ** BDR for PostGRE SQL
     ** Challenges with ACTIVE-ACTIVE
        ** What if data is modified concurrently in both Data center's
        ** Handling write conflicts
            ** Know approaches
               1) Each write has a uniqueID and pick the write with the highest ID as winner and throw away others. If Time stamp is used this technique 
                  is known as **last write wins**
               2) Give each replica a unique ID and let writes that originate at higher number replica win. Again this is dangerous for data loss
               3) Record conflict in a data structure and write app code to resolve the conflict at some later time.
   
        ** How to build a custom conflict resolution logic
               a) ON WRITE: As db detects a conflict in log of replicated changes it calls a conflict handler
               b) ON READ: when conflict is detected on READ , databases like CouchDB send multiple versions of data to application and application may 
                  prompt the user to automatically resolve the conflict
               c) Operational transformation is a conflict resolution algorithm behind Google docs and Etherpad
   
     ** Multi leader replication topologies
           ** If you have two leaders, there is only one plausible topology i.e. leader 1 must send all writes to leader 2 and vice versa.
           ** With more than 2 leaders various different topologies are possible
               a) Circular
               b) STAR
               c) All-to-All topology
           ** MySQL by default uses circular topology : Each node receives writes from another node and forwards those writes plus any of its own writes 
              to next node
           ** Most general topology is all to all i.e. every leader sends writes to every other leader
           ** A problem wit Circular and STAR topologies is that if just one node fails it can interrupt the flow of replication messages
           ** Disadvantages with ALL-TO-ALL 
              ** Some n/w links may be faster than others so some replication messages may over take others , we use **version vectors** to solve the over 
                 taking problem
   
    **Leaderless Replication**
           ** Cassandra uses leaderless replication and this style is based on dynamo style db
           ** In some leaderless implementations , the client directly sends its write to several replicas, in some others coordinator node does this on 
              behalf of the clients
           ** Similarly reads are also sent to multiple nodes / replicas. The client may get different responses from diff nodes i.e. Up to date values 
              from one node and stale from another , version vectors are used to solve this conflict
           ** Read repair : When client gets stale value from one of the replica it writes new values from other replica back to the replica with stale 
              value , this approach works well for values that are frequently read
           ** Anti entropy: Some data stories there is a background process that constantly looks for differences in data b/w replicas and copies any 
              missing data from one replica to another. Without anti entropy, values that are rarely read may be missing from some replicas  and thus have 
              reduced durability because read repair is performed only when a value is read by the application
           ** Quorum:  Minimum number of votes required for a read / write to be valid. In Dynamo style db the parameters are typically configurable
                     ** r+w <- n , this is possible configuration for high availability and low latency at reduced durability ( data could be stale). 
                     ** Dynamo style databases are generally optimized for uses cases that can tolerate eventual consistency
           ** Databases with leader less replication are best suited for use cases that require high availability and low latency and that can tolerate                 occasional stale reads                    
           ** Sloppy quorums: This is a temporary fix while the n/w glitch is corrected. Clients can connect to some database nodes during n/w 
                              interruption , not just to the nodes that are part of quorum such that reads/writes are accepted
           ** hinted hand off: Once a n/w glitch is corrected writes are handed off to home nodes i.e. nodes part of the n/w. 
           ** Sloppy quorums are optional in all common Dynamo implementations. In Cassandra this is disabled by default                    
   
**Multi Data center operation**
                               
    ** Cassandra and Voldermort implement multi data center support with normal leader less model.
    ** Each write from client is sent to all replicas regardless of data center, but the client usually only waits for acknowledgment from a quorum of 
       nodes  within its local data center to achieve low latency. The higher latency writes to other data centers are often configured to happen 
       asynchronously
    ** LWW ( Last write wins)
         ** Only supported conflict resolution algorithm in Cassandra
         ** Attach a time stamp to each write, pick the biggest time stamp as most recent and discard any writes with an earlier time stamp. 
         ** This is a good convergence but at the cost of durability
         ** If loosing data is not acceptable LWW is a poor choice for conflict resolution
         ** Safe way of using LWW is to ensure that a key is only written once and is immutable. In Cassandra a recommended way is to use UUID as key and 
            thus giving each write a unique key
    ** Version Vectors : 
         ** Also called Vector clock
         ** Ensures that it is safe to read from one replica and subsequently write back to another replica. Doing so may result in sibling being created 
            but no data is lost as siblings are merged correctly. 
         ** Involves a overhead on client
         ** In simple words when comparing the state of replicas, version vectors are the right data structures to use
 
                               
**Partitioning and Replication**
                               
    ** Even though each record belongs to exactly one partition , it may still be stored on several nodes for fault tolerance
    ** A node may store more than one partition
    ** Each node may be a leader for some partitions and a follower for other partitions
    ** If records are only ever accessed via the primary key, we determine the partition from the key and use it to route read and write requests to the          partition responsible for that key
    ** Challenges with secondary indexes and partitoning
       **  Usually doesn't identify a record uniquely but rather is a way of searching for occurances of a particular value
       ** They don't map neatly to partitions
       ** There are 2 approaches of partitioning a db with secondary indexes
                  ** Document based partitioning : 
                               ** Each partition is completely separate. Each partition maintains its own secondary indexes covering only 
                                  documents in that partition. It doesn't care what does is stored in other partitions
                               ** If you want to read we need to query all partitions. This can lead to tail latency amplification.
                               ** Recommendation is to structure partition schemes so that secondary index queries can be served from single partition
                        
                  ** Term based partitioning : 
                               ** Rather than each partition having its own local index , a global index that covers data in all the partitions
                               ** To cover load the global index must also be partitioned
                               ** The advantage is that reads are more efficient, there is no scatter gather over all partitions. Clients only needs to 
                                  make a request to the partition containing the term it wants
                               ** Down side is that writes are slower because single write to a document affects multiple partitions of index i.e. all 
                                  the columns that are part of secondary index
                 ** Summary: Design your indexes / partitions based on query pattern
                 ** In practice updates to global secondary indexes are often asynchronous, the change you made may not be reflected on the index 
                    immediately       
                            
                  
 
                               
  ![Screen Shot 2022-08-04 at 3 05 38 PM](https://user-images.githubusercontent.com/7702406/182960820-c9f80b82-fdfa-4e7c-9e2f-32317fde7217.png)
   
**Rebalancing**
                        
     ** Process of moving load from one node in the cluster to another is called rebalancing
     ** Rebalancing strategies:
               ** Hash Mod N : Not recommended
               ** Fixed number of partitions : 
                            ** Create many more partitions than there are nodes and assign several partitions to each node
                            ** Best performance is achieved when the size of the partition is just right , neither too big nor too small
              ** Dynamic partitioning :
                            ** When partition grows to exceed a configured size ( For HBASE its 10GB), it is split into two partitions so that 
                               approximately half of the data end on each side of the split
                            ** If data is deleted partitions are merged with adjacent. This adapts well to total data volume
                            ** Each partition is assigned to one node and each node can handle multiple partitions
                            ** HBase transfers partitions through HDFS
                            ** This approach can work well with both key range and hash partitioned data
                             
                                       
**Request routing**
                        
     ** If I want to read or write a key ex: foo which IP address and port no do I need to connect to... This is where we plug in service discovery
     ** Any piece of software that is accessible over a network especially if its aiming for high availability has this problem. Many companies have 
        their in house service discovery tools
     ** One way to solve this to build a routing tier that acts as a partition aware load balancer OR 
     ** Clients being aware of paritions and the assignment of partitions
     ** Many distributed systems rely on separate coordination services such as zoo-keeper to keep track of cluster metadata. Each node registers itself 
        in zookeeper and zoo keeper maintains the authorization mapping of partition to nodes
     ** Actors such as routing tiers or the partition aware client can subscribe to this info in zoo-keeper
     ** When ever a partition changes ownership or a node is added or removed Zookeeper notifies the routing so thatit can keep its routing information 
        upto date
     ** Espresso's Helix for cluster manamgement inturn relies on Zoo keeper
     ** Kafka uses zoo keeper to track partition assignments
     ** Cassandra uses gossip protocol
     ** Couchbase doesn't rebalance automatically
                        
  **Transactions**
 
    ** Fault tolerance mechanisms in Databases
            ** BASE : Basically available soft state and eventual consistency
            ** ACID : Atomicity , Consistency , Isolation and Durability
                        ** Atomicity: The ability to abort a transaction on error and have all writes from that transactoin discard is the defining 
                                      feature of ACID. Database provides a all or nothing guarantee. The database saves you from having to worry about 
                                      partial failures by giving an all or nothing guarantee
                        ** Consistency: Certain statements about your data must always be true. If a transaction starts with a database that is valid     
                                      according to some invariants and so any further writes preserve its invariants
                        ** Isolation: Concurrently executing transactions are isolated from each other , they cannot step up on each other toes. The 
                                      database ensures that when transactions have comitted , the result is the same as if they had run serially though 
                                      in reality they may have run concurrently. If one of the transaction makes several writes, then another transaction 
                                      should see either all or none of the writes but not subset            
                        ** Durability: Durability is a promise that once a transaction has committed successfully any data it has written will not be 
                                      forgotten even if there is hardware fault or database crashes
    ** Dirty read : One transactions reads another transactions uncomitted data
    ** Many distributed data stores have abondoned multi object transactions because they are difficult to implement across partitions and they 
    can get in the way in some scenarios where high availability or performance is required             
    ** Transactions are very useful in this situation to prevent denormalized data from going out of sync. If we want to make sure that several different 
    systems either commit or abort together two phase commit can help
    ** Transaction isloation:  Databases have long tried to hide concurrency issues from application developers by providing transaction isolation. In 
    theory should make life easier by pretending that no concurrency is happening
    ** Serializable isolation means that the db guarantees that transactions have the same effect as if they have ran serially. This has performance cost     so we use weak isolation and it protects against some concurrency issues
    ** Read comitted:
                        ** Most basic level 
                        ** Makes two guarantees i.e. no dirty reads and no dirty writes
                        ** Dirty reads:  Read data that has been committed.
                        ** Dirty writes: Over write data that has been comitted/ Any writes by a transaction only become visible to others when that 
                           transaction commits
                        ** Prevents dirty writes by using row-level locks
                        ** Row level locks:
                                   ** Automatically done by databases in read committed  mode
                                   ** One long running write transaction can force many read only transactions to wait , they harm response times. So to 
                                      avoid this issue most databases prevent dirty reads by remembering both the old committed value and the new value 
                                      set by transaction that currently holds the write lock. While the transaction is going on other read transactions 
                                      are simply given old value   
                       ** Non repeatable read or read skew : 
                                   ** Snapshot isolation is the most common solution
                                   ** Each transaction reads from a consistent snapshot of the database , that is the transaction sees all the data that 
                                      was committed in the database at the start of the transaction. Even if the data is subsequently modified by 
                                      another transaction each transaction only sees the old data from that particular point in time
                                   ** Snap shot isolation us supported by PostgreSQL , MySQL with InnoDB storage
                                   ** The key principle for snapshot isolation is that readers never block writers and writers never block readers , so                                         long running queries are not impacted
                                   ** Snapshot isolation is also known as MVCC ( Multi versioned concurrency control )
                                   ** By never updating values in place but instead creating a new version any time a value is changed the database can                                         provide a consistent snapshot with little over head
                                   ** Snapshot isolation is a useful isolation level especially for read only transactions
                                   ** In Oracle db it is called Serializable
                                   ** In MySQL it is called repeatable read
                                   ** Snapshot isolation needs a background process for compaction and garbage collection
                       ** Read comitted and snapshot isolation for what a read only transaction can see in the presence of concurrent writes. Other 
                          problems like lost update problem can also occur i.e. if two transactions concurrently do read modify write then one of the 
                          modifications can be lost
                            
                       ** How to handle the lost update problem :
                                  ** The solution is a)atomic writes and b) Explicit locking
                                  ** Atomic writes are also known as cursor stability. In atomic writes we express code in terms of atomic operations
                                  ** Implemented by taking an exclusive lock on the object when it is read so that no transaction can read it until the 
                                     update is applied
                                  ** Explicit locking : Application to explicitly lock objects that are going to be updated so that when T1 is on T2 is 
                                     forced to wait
                                  ** Automatically detecting lost updates : Alternative is to allow the transactions to execute in parallel and if 
                                     Transaction manager detects a lost update, abort the transaction and force it to retry its read->modify->write cycle
                                  ** Postgre SQL's repeatable read , Oracle's Serializable and SQL Server's snapshot isolation -> Automatically detect 
                                     when a lost update has occured and abort transactions
                                  ** MySQL's with Inno DB repetable read doesn't detect lost updates
                                  ** For Lost update detection it doesn't require application code to use any special database feature
          
                       ** Compare and Set :
                                  ** In DB Engines that don't provide transactions 
                                  ** Purpose of this operation is to avoid lost updates by allowing an update to happen only if the va;ue has not changed 
                                     since you last read it. If the current value doesn't match what you previously read the update has no effect and the 
                                     read modify write cycle must be retried

                       ** Replication and conflict resolution :
                                  ** For databases with multileader or leaderless replication techniques based on locks or compare and set don't apply
                                  ** Common approach in such replicated databases is to allow concurrent writes to several conflicting versions of a 
                                     value and to use application code or special data structures to resolve and merge the versions
   
                       ** Dirty writes and lost updates - 2 kinds of race conditions that can occur when different transactions concurrently try to write 
                          to same object                      
                       
                       ** Write Skew: Neither dirty write nor lost update because the two transactions are updating two different objects
                                 ** Only solution is True Serializable solution
                                 ** Not automatically detected in PostGreSQL's repeatable read , MYSQL's InnoDB repeatable read and SQL Server's snap                                         shot isolation level
                                 ** If you cannot use Serializable isolation, the second best option is to explicitly lock the rows that the transaction 
                                    depends on. Ex: Use of FOR UPDATE tells the database to lock all rows returned by the query
                       ** Under snapshot isolation we cannot prevent two users from claiming usernames so applying unique constraints is the best
                       ** Phantom : Write in one transaction changes the result of a search query in another transaction
                       ** Snapshot isolation avoids phantoms in read only queries but in read write transactions phantoms can lead to write skew
                       ** Materializing  conflicts :
                                 ** Disadvantage is that concurrency control mechanism leaks into application data model
                                 ** Takes a phantom and turns into a lock
                                 ** Should be the last resort
                       ** Summary : Serializable isolation is much preferred option in most cases          
                                 
                        
                        
                        
                        
**Latency**
      
    ** If you've users around the world , you might want to have servers at various locations world wide so that each user can be served from a data     
       center that is geographically close to them. This avoid the users having to wait for N/W packets to travel halfway around the world
   
**Challenges with Distributed systems**
   
    **  Anything that can go wrong will go wrong   
    **  Some parts of the sytem that are broken in some unpredictable way is partial failure. Difficulty is that these are non-deterministic
    **  Super computer - Just like a single node computer it deals with partial failure by letting it escalate into total failure
    **  Fault handling must be part of software design and we need to know the behavior to expect from software in case of a fault
    **  Building a reliable system from unreliable components is part of Software Engineering. Ex: Internal Protocol is unreliable it may drop delay 
        duplicate or reorder packets. TCP provides a more reliable transport layer on top of IP. Ensure missing packets are retransmitted, duplicates   
        are eliminated and packets are reassembled in the order they were sent
    **  TCP can hide packet loss, duplication and reordering from you but it cannot magically remove delays in the N/w
    ** Shared nothing is not the only way of building systems but it has become the dominant approach for building internet services. Its comparitively          cheap because it requires no special hardware. It can make use of commodity cloud computing services and can achieve high reliability through  
       redundancy across multiple geographically distributed data centers
    ** Internet and most internal networks in data centers are asynchronous packet n/w i.e. one node can send a message to another but n/w gives no 
       guarantees as to when it will arrive or whether it will arrive. In these if you send a request to another node and don't receive a response it is 
       impossible to tell why. The usual way of handling is through time outs i.e. after some time you give up waiting and assume that the response is 
       not going to arrive.
    ** In a medium size DC there are 12 Network faults per month
    ** A network interface that some timesdrops all inbound packets but sends out bound packets successfully so just bcoz a n/w links works in one 
       direction and it doesn't guanrantee its also working in opposite direction
    ** When one part of the network is cut off from the rest due to network fault it is called Network PArtition or Net split
    ** When a node is declared dead its responsibilities need to be transferred to other nodes which places additional load on other nodes and the 
       network. IF the system is already struggling with high load declaring nodes prematurely can make the problems worse
    ** Most systems we work with have unbounded delays , that is they try to deliver packets as quickly as possible but there is no upper limit on the 
       time it may take for packet to arrive   
    ** TCP performs flow control ( Also known as Congestion avoidance) or back pressure in which a node limits its own rate of sending inorder to avoid 
       overloading a n/w link  
    ** In public clouds and multi tenant data centers resources are shared among customers. Network delays can be highly variable if someone near you ( a 
       noisy neighbor)   is using a lot of resources
    ** Unreliable clocks 
            ** Each machine has its own notion of time
            ** NTP stands for Network Time protocol
            ** NTP is a mechanism to synchronize clocks . Allows the computer clock to be adjusted according to the time reported by group of servers. 
               These servers inturn get time from a GPS receiver
            ** NTP allows the clock rate to be speeded up or showed down by 0.05%
            ** Different kinds of clocks
                   ** Time of the day clock : Usually syncrhonized with NTP
                   ** Monotonic clock: In distributed systems using a monotonic clock for elapsed time is usually fine because it doesn't assume any
                      synchronization b/w diff node's clocks
            ** Quartz clock in a computer is not very accurate it drifts ( runs faster or slower )
            ** Clock drift varies depending on the temparature of the machine
            ** Google assumes a clock drift of 200 PPM ( parts per million), equivalent to 6ms drift for a clock that is synchronized every 30s or 17s
               drift that is synchronized once a day
    ** Software must be designed on the assumption that network will occassionally be faulty and s/w must handle such faults gracefully. The same is true
       with clocks although they work quite well  most of the time. Robust software needs to be prepared to deal with incorrect clocks
    ** If you use Software that requires synchronized clocks it is essential that you also carefully monitor the clock offset b/w all the machines
    ** Logical clocks: Based on incrementing counters rather than oscillating quartz crystal are safer alternatives for ordering events
    ** Google deploys. GPS reciever or atomic clock in each data center allowing clocks to be synchronized to within about 7ms            
   
 
 **Garbage collection in Java**
    
    ** Thread pausing : OS Context switching to another thread or Hypervisor switches to different VM or GC pauses or IO pauses or OS Paging
    ** Limiting the impact of GC :
           ** Treat GC pauses as outages and to let other nodes handle requests from clients
           ** If run time can warn the application that a node soon requires GC pause the application can stop sending requests to the node
           ** Another way is to Garbage collect only for short lived objects and to restart the process periodically
   
**Fencing Tokens**

      ** When using a lock or lease to protect access to some resource such as file storage we need to ensure that a node that is under a false belief of 
      being leader cannot disrupt the system
   
**Byzantine faults**

     ** A node may claim to have received a particular message when in fact it didnot
     ** A system is Byzantine fault tolerant if it continues to operate correctly even if some of the nodes are malfunctioning and not obeying the  
        protocol or if malicious attackers are interfering with the network
   
**Consistency and Consensus**

    ** Linearizability : Strongest consistency model in common use. Gives an illusion to client that there is only one replica, then every client would 
      have the samew view of the data and you wouldn't worry about replication lag
    ** Linerizability is also known as atomic consistency , strong consistency or external consistency
    ** Linerizability is a recency guarantee. In a linerizable system as soon as one client successfully completes a write all clients reading from that        database must be able to see the value just written
    ** Query returning a stale result is a violation of Linerizability
    ** Basic idea behind linearizability is simple to make a system appear as if there is only a single copy of data
    ** A database may provide both serializability and linearixability and thus is known as Strict Serializability or Strong one copy serializability
    ** Where to use Linearizability :
    
          ** A system that uses single leader replication needs to ensure that there is only one leader ( not split brain )
          ** One way of electing leader is to use a lock; every node that starts up tries to acquire a lock and the one that succeeds becomes a leader
          ** No matter how the lock is implemented it must be Linearizable. All nodes must agree on who owns the lock
          ** Zoo keeper and etcd are often used to implement distributed locks and leader election. They use consensus algorithms to implement 
             Linearizable operations ina fault tolerant way
          ** Distributed locking is also used at much granular level to implement Linearizable operations   
   
     

**SSL/TLS Handshake - How it works ?**
    
         ** SSL/TLS is of two types : a) One way SSL b) Two way SSL
         
         ** One Way SSL:
              ** Server proves its identity and this is usually the setup in most of the enterprises.
              ** In one way SSL, only client validates the server to ensure that it receives data from the intended server. For implementing one-way SSL, 
                 server shares its public certificate with the clients.
                 
         ** Two Way SSL :
              ** Both Client and Server needs to prove their identities and this is not a typical setup
              ** Contrary to one-way SSL; in case of two-way SSL, both client and server authenticate each other to ensure that both parties involved in 
                 the communication are trusted. Both parties share their public certificates to each other and then verification/validation is performed 
                 based on that.
              
         ** SSL / TLS is a multi step approach however at a very high level in a one way SSL where Server proves it identity , it presents its  
            Certificate  to Client before connection is opened. The Client looks at the signer / ROOT CA of the certificate presented by Server 
            in its  trust/key store and if valid  establishes a secure connection
            
         ** In a client server application , client can rely on a default key store or choose to over ride the cert store.
            In BE applications developed using Java like a REST Webservice talking to a database on cloud in order for the
            BE to trust the identity of the database server on cloud the client can rely on a default key store provided by Oracle inside JDK in a 
            cacerts folder. So the client can choose to use this key store which contains the Root CA of all major certifying authorities. 
            Alternatively the Java application can use a custom key store or trust store and pass them in the JAVA_OPTS using the two parameters as below
   
                 -Djavax.net.ssl.trustStorePassword=<password of the trust or key store>
                 -Djavax.net.ssl.trustStore=/app/.certs/<applications own cert store like rapidssl.jks>   
    
         ** Java automatically looks for a certificate (or its root CA) in its keystores for all TLS requests.
            If it’s found, the TLS handshake will be successfully established
    
         ** What is SSL and What is TLS and what are the differences?      
    
         ** What is trust store ? TrustStore setup is required for the successful connection at the client side and doesn't contain  private and 
            sensitive information. Also TrustStore holds the certificates of external systems that you trust.
    
         ** What is key store ? Keystore contains private and sensitive information.
    
         ** Keystores and truststores are repositories that contain cryptographic artifacts like certificates and private keys that are used for       
           cryptographic protocols such as TLS. A keystore contains personal certificates, plus the corresponding private keys that are used 
           to identify the owner of the certificate.
    
         ** How can we debug TLS/SSL handshake from a Java application ?              
              https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html
    
         ** How does an application built in Java connect with a remote server and establish its identity ? What are the options ?
            keyStore will be used to store server certificates which the server will present to the client on SSL connection.
    
         ** Command to view a key store : 
                ** $keytool -list -v -keystore <nameofthekeystore.jks> 
                
         ** To View a key store we can also use Key Store Explorer tool from : https://keystore-explorer.org/downloads.html
         ** The cacerts file is a collection of trusted certificate authority (CA) certificates. Oracle includes a cacerts file with its SSL support in  
            the Java™ Secure Socket Extension (JSSE) tool kit and JDK. It contains certificate references for well-known Certificate authorities, such as             VeriSign™. Its format is the "keystore" format defined by Oracle. An administrator can edit the cacerts file with a command line tool (also   
            provided by Oracle) called keytool
  
         ** How does TLS SSL provide security ?
         ** References: https://dzone.com/articles/ssl-in-java

**How to debug Production issues**
    
         ** JDK Flight Recorder is a system for collecting diagnostic and profiling data about a running Java application. Integrated into the Java 
            Virtual Machine (JVM) with extremely low performance overhead, it is designed for profiling heavily loaded production environments. Run JFR , 
            stands for Java Flight recording
         ** JFR is available since Java 12
         ** //TODO Understand Java Mission Control and understand the basics

**Architectural landscape patterns**   

         ** Monolith
         ** N-Tierd
         ** Serverless
         ** SOA
         ** Microservices
         ** Peer to Peer

 **Architectural structured patterns**
 
         ** Layered
         ** Microkernel
         ** CQRS
         ** Event sourcing
         ** CQRS + Event sourcing
