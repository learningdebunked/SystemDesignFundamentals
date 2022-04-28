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
