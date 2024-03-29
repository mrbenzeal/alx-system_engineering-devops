# Web Infrastructure Design

Project done during **Full Stack Software Engineering studies** at **ALX**. It aims to learn about how to design a Web Infrastructure.

## Key concepts

* [Network basics](https://alx-intranet.hbtn.io/concepts/33)
* [Server](https://alx-intranet.hbtn.io/concepts/67)
* [Web Server](https://alx-intranet.hbtn.io/concepts/17)
* [Application Server](https://www.gartner.com/en/information-technology/glossary/application-server#:~:text=An%20application%20server%20is%20a,applications%20on%20the%20third%20side.)
* [Application server vs Web server](https://www.nginx.com/resources/glossary/application-server-vs-web-server/)
* [What’s the difference between a Web server and an App server?](https://www.youtube.com/watch?v=S97eKyv2b9M)
* [DNS](https://alx-intranet.hbtn.io/concepts/12) 
* [DNS record types](https://pressable.com/?s=DNS&post_type=knowledgebase)
* [Load balancer](https://alx-intranet.hbtn.io/concepts/46)
* [Monitoring](https://alx-intranet.hbtn.io/concepts/13)
* [Database](https://searchdatamanagement.techtarget.com/definition/database)
* [Single point of failure](https://en.wikipedia.org/wiki/Single_point_of_failure)
* [HTTP & HTTPS](https://www.instantssl.com/http-vs-https)
* [Firewall](https://www.webopedia.com/definitions/firewall/)
* [High availability cluster (active-active/active-passive)](https://docs.oracle.com/cd/E17904_01/core.1111/e10106/intro.htm#ASHIA712)
* [How to avoid downtime when deploying new code](https://softwareengineering.stackexchange.com/questions/35063/how-do-you-update-your-production-codebase-database-schema-without-causing-downt#answers-header)

## File Descriptions

Each file contains a link to an image hosted on Imgbox. These images are based on the following requirements: <br />

##  Tasks

### [0-simple_web_stack](0-simple_web_stack)

###  Description

This is a simple web infrastructure that hosts a website that is reachable via `www.foobar.com`. There are no firewalls or SSL certificates for protecting the server's network. Each component (database, application server) has to share the resources (CPU, RAM, and SSD) provided by the server.

On a whiteboard, design a one server web infrastructure that hosts the website that is reachable via `www.foobar.com.` Start your explanation by having a user wanting to access your website. <br />

You must use:

* 1 physical server

* 1 web server (Nginx)

* 1 application server

* 1 application files (your code base)

* 1 database (MySQL)

* 1 domain name `foobar.com` configured with a `www` record that points to your server IP `8.8.8.8`

### Specifics About This Infrastructure

+ What a server is.<br/>A server is a computer hardware or software that provides services to other computers, which are usually referred to as *clients*.

+ The role of the domain name.<br/>To provide a human-friendly alias for an IP Address. For example, the domain name `www.wikipedia.org` is easier to recognize and remember than `91.198.174.192`. The IP address and domain name alias is mapped in the Domain Name System (DNS)

+ The type of DNS record `www` is in `www.foobar.com`.<br/>`www.foobar.com` uses an **A record**. This can be checked by running `dig www.foobar.com`.<br/>**Note:** the results might be different but for the infrastructure in this design, an **A** record is used.<br/>
<i>Address Mapping record (A Record)—also known as a DNS host record, stores a hostname and its corresponding IPv4 address.</i>

+ The role of the web server.<br/>The web server is a software/hardware that accepts requests via HTTP or secure HTTP (HTTPS) and responds with the content of the requested resource or an error messsage.

+ The role of the application server.<br/>To install, operate and host applications and associated services for end users, IT services and organizations and facilitates the hosting and delivery of high-end consumer or business applications

+ The role of the database.<br/>To maintain a collection of organized information that can easily be accessed, managed and updated

+ What the server uses to communicate with the client (computer of the user requesting the website).<br/>Communication between the client and the server occurs over the internet network through the TCP/IP protocol suite.

### Issues With This Infrastructure

+ There are multiple SPOF (Single Point Of Failure) in this infrastructure.<br/>For example, if the MySQL database server is down, the entire site would be down.

+ Downtime when maintenance needed.<br/>When we need to run some maintenance checks on any component, they have to be put down or the server has to be turned off. Since there's only one server, the website would be experiencing a downtime.

+ Cannot scale if there's too much incoming traffic.<br/>It would be hard to scale this infrastructure becauses one server contains the required components. The server can quickly run out of resources or slow down when it starts receiving a lot of requests.


### [1-distributed_web_infrastructure](1-distributed_web_infrastructure)

### Description

This is a distributed web infrastructure that atttempts to reduce the traffic to the primary server by distributing some of the load to a replica server with the aid of a server responsible for balancing the load between the two servers (primary and replica).

You must add to [0-simple_web_stack](0-simple_web_stack):

* 2 physical servers

* 1 web server (Nginx)

* 1 application server

* 1 load-balancer (HAproxy)

* 1 application files (your code base)

* 1 database (MySQL)

### Specifics About This Infrastructure

+ The distribution algorithm the load balancer is configured with and how it works.<br/>The HAProxy load balancer is configured with the *Round Robin* distribution algorithm. This algorithm works by using each server behind the load balancer in turns, according to their weights. It’s also probably the smoothest and most fair algorithm as the servers’ processing time stays equally distributed. As a dynamic algorithm, *Round Robin* allows server weights to be adjusted on the go.
+ The setup enabled by the load-balancer.<br/>The HAProxy load-balancer is enabling an *Active-Passive* setup rather than an *Active-Active* setup. In an *Active-Active* setup, the load balancer distributes workloads across all nodes in order to prevent any single node from getting overloaded. Because there are more nodes available to serve, there will also be a marked improvement in throughput and response times. On the other hand, in an *Active-Passive* setup, not all nodes are going to be active (capable of receiving workloads at all times). In the case of two nodes, for example, if the first node is already active, the second node must be passive or on standby. The second or the next passive node can become an active node if the preceding node is inactive.
+ How a database *Primary-Replica* (*Master-Slave*) cluster works.<br/>A *Primary-Replica* setup configures one server to act as the *Primary* server and the other server to act as a *Replica* of the *Primary* server. However, the *Primary* server is capable of performing read/write requests whilst the *Replica* server is only capable of performing read requests. Data is synchronized between the *Primary* and *Replica* servers whenever the *Primary* server executes a write operation.
+ The difference between the *Primary* node and the *Replica* node in regard to the application.<br/>The *Primary* node is responsible for all the write operations the site needs whilst the *Replica* node is capable of processing read operations, which decreases the read traffic to the *Primary* node.

### Issues With This Infrastructure

+ There are multiple SPOF (Single Point Of Failure).<br/>For example, if the Primary MySQL database server is down, the entire site would be unable to make changes to the site (including adding or removing users). The server containing the load balancer and the application server connecting to the primary database server are also SPOFs.
+ Security issues.<br/>The data transmitted over the network isn't encrypted using an SSL certificate so hackers can spy on the network. There is no way of blocking unauthorized IPs since there's no firewall installed on any server.
+ No monitoring.<br/>We have no way of knowing the status of each server since they're not being monitored.


### [2-secured_and_monitored_web_infrastructure](2-secured_and_monitored_web_infrastructure)

### Description

This is a 3-server web infrastructure that is secured, monitored, and serves encrypted traffic.

On a whiteboard, design a three servers web infrastructure that host the website `www.foobar.com`, it must be secured, serve encrypted traffic and be monitored. <br />

You must add to [1-distributed_web_infrastructure](1-distributed_web_infrastructure):

* 3 firewalls

* 1 SSL certificate to serve `www.foobar.com` over HTTPS

* 3 monitoring clients (data collector for Sumologic or other monitoring services)

### Specifics About This Infrastructure

+ The purpose of the firewalls.<br/>The firewalls are for protecting the network (web servers, anyway) from unwanted and unauthorized users by acting as an intermediary between the internal network and the external network and blocking the incoming traffic matching the aforementioned criteria. 
+ The purpose of the SSL certificate.<br/>The SSL certificate is for encrypting the traffic between the web servers and the external network to prevent man-in-the-middle attacks (MITM) and network sniffers from sniffing the traffic which could expose valuable information. The SSL certs ensure privacy, integrity, and identification.
+ The purpose of the monitoring clients.<br/>The monitoring clients are for monitoring the servers and the external network. They analyse the performance and operations of the servers, measure the overall health, and alert the administrators if the servers are not performing as expected. The monitoring tool observes the servers and provides key metrics about the servers' operations to the administrators. It automatically tests the accessibility of the servers, measures response time, and alerts for errors such as corrupt/missing files, security vulnerabilities/violations, and many other issues. 

### Issues With This Infrastructure

+ Terminating SSL at the load balancer level would leave the traffic between the load balancer and the web servers unencrypted.
+ Having one MySQL server is an issue because it is not scalable and can act as a single point of failure for the web infrastructure.
+ Having servers with all the same components would make the components contend for resources on the server like CPU, Memory, I/O, etc., which can lead to poor performance and also make it difficult to locate the source of the problem. A setup such as this is not easily scalable.


### [3-scale_up](3-scale_up)

### Description

This web infrastructure is a scaled up version of the infrastructure described Secured and Monitored Web Infrastructure. In this version, all SPOFs have been removed and each of the major components (web server, application server, and database servers) have been moved to separate GNU/Linux servers. The SSL protection isn't terminated at the load-balancer and each server's network is protected with a firewall and they're also monitored.

You must add to [2-secured_and_monitored_web_infrastructure](2-secured_and_monitored_web_infrastructure):

* 1 physical server

* 1 load-balancer (HAproxy) configured as cluster with the other one

* Split components (web server, application server, database) with their own server

### Specifics About This Infrastructure

+ The addition of a firewall between each server.<br/>This protects each server from unwanted and unauthorized users rather than protecting a single server.

### Issues With This Infrastructure

+ High maintenance costs.<br/>Moving each of the major components to its own server, means that more servers would have to be bought and the company's electricity bill would rise along with the introduction of new servers. Some of the company's funds would have to be used to buy the servers and pay for the electricity consumption needed to keep the servers (including the new and old ones) running.


## Files

| Filename | Description |
| -------- | ----------- |
| [`0-simple_web_stack`](./0-simple_web_stack)  | Web Infrastructure Design with a LAMP stack. This contains: 1 server, 1 web server, 1 application server, 1 database and 1 domain name |
| [`1-distributed_web_infrastructure`](./1-distributed_web_infrastructure) | Web Infrastructure Design, based on `0-simple_web_stack` that contains some additional components: 1 server, 1 web server, 1 application server, 1 load-balancer, 1 set of application files, 1 database |
| [`2-secured_and_monitored_web_infrastructure`](2-secured_and_monitored_web_infrastructure) | Web Infrastructure Design, based on `1-distributed_web_infrastructure` that contains some additional components: 3 firewalls, 1 SSL certificate, 3 monitoring clients |
| [`3-scale_up`](3-scale_up) | Web Infrastructure Design, based on `2-secured_and_monitored_web_infrastructure` that contains some additional components: 1 server, 1 load-balancer |


## Authors :black_nib:

[Ani Obinna Benzeal](https://www.linkedin.com/in/engraniobc/) | [Twitter](https://twitter.com/AniBenzeal) | [GitHub](https://github.com/mrbenzeal)
