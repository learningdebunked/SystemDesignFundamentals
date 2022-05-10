# System Design Fundamentals

**_How does DNS work:_**

The goal is to resolve a fully qualified domain name into a IP Address.  At each step , the corresponding components in this diagram look into their cache first. For instance client first looks into browser cache and operating system cache. Similarly DNS Recursive resolver also looks into its cache.
Similarly Root name server and TLD server as well.
DNS resolution can happen two ways: 
1) Recursive and
2) Iterative

The Recursive query is between a DNS client and its local DNS server.Iterative query is  between a local DNS server and other DNS servers and Dont demand a name resolution, which means other DNS servers may respond  a name resolution if they know or simply respond with a referral 

The flow can be imagined with an example:
Say I type www.google.com on my web browser. The browser will check two places first to see if there is any previous name resolution record of this google machineone place is computer's cache memory . On windows it is ipconfig/displaydns and other place is a simple text file called Hosts
Suppose the record doesn't exists. Then the DNS client queries the local DNS Server which is a recursive query,to resolve a name is the responsibility of my local DNS Server 




![Screen Shot 2022-04-28 at 7 35 10 AM](https://user-images.githubusercontent.com/7702406/165776998-f824b2bf-7b7e-4444-84ba-44ff55dd358e.png)

**What the heck is Socket? Connection ? Http ? Https ? rpc ? Rest?**

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

What is Cache busting?
This is a mechanism to tell the browser that new version of the file is available. So browser doesn't retrieve the old file from Cache but rather makes request to origin server for new file. The advantage of this is that users receive the most recently updated file without having to perform a hard refresh or clear browser cache

There are three ways to implement Cache busting:

1) File name versioning
2) File Path versioning
3) Query strings 

Option 3 Above is not recommeneded because some proxies & CDNs are not able to cache

The recommended ways are **File name versioning and File Path versioning.**

Dyanamic Content Caching: The idea behind Dynamic content caching is that not all info on the page is same for all users. Some elements are personalized either based on Geography i.e. location , time in day , device types etc.

Dynamic content is generated by scripts that change the content of the page by running scripts in CDN Cache.

Scripts can be run on the CDN cache such that Dynamic content is generated and served to clients or static content be altered , cached or purged from Cache


API Gateway: Traditional architectures relied on clients making direct calls to the BE APIs. The disadvantage of this is a force upgrade of the client which is not a desirable experience. There a two ways to mitigate this 1) BFF {backend for front end }, similar to API Gateway or an extension to API Gateway and 2) a plain API Gateway

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
4)


**What is Consistent Hashing?
What is consistent hashing with Vnodes?**

**What is CAP Theorem?
What is PACELC Theorem?**

**Microservices and patterns.**
1) What is side car pattern? What are the other patterns ? Why is sidecar famous?
2) What is istio ?
3) What is service mesh? How is it implemented? What are the advantages it provides?
4) How distributed transactions work in MicroServices architecture?
5) What is 2PC?
6) What is 3PC?
7) What is SAGA? 
8) When to use 2PC vs 3 PC vs Saga ? What are the advantages and disadvantages?





