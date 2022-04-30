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

_**How does a Load balance work:**_

What is a load balancer?
Load balancer is a REVERSE PROXY maintaining / managing a pool of application servers.

What is a REVERSE PROXY?
Load balancer is called Reverse Proxy because it acts as a proxy server between the client and the application server.

Similarly a proxy for Client is known as a Forward Proxy. 

Key terms:
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
Examples of CDN:
1) Why are CDN required ?
2) What are the benefits of using a CDN ?
3) What are the disadvantages of using a CDN ?
4) What are the types of CDN ?
   There are two types of CDN. A pull CDN and a Push CDN
6) What scenarios do we use a CDN?


