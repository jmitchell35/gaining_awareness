# Web Infrastructure Design Documentation

This comprehensive guide covers the fundamental concepts of web infrastructure, from networking basics to advanced configurations for high availability, security, and monitoring.

## Table of Contents
- [Network Basics](#network-basics)
- [Servers](#servers)
- [Web Servers](#web-servers)
- [DNS (Domain Name System)](#dns-domain-name-system)
- [Load Balancers](#load-balancers)
- [Monitoring](#monitoring)
- [Databases](#databases)
- [System Redundancy](#system-redundancy)
- [Security Components](#security-components)
- [Web Stack Acronyms](#web-stack-acronyms)

## Network Basics

### What is a Protocol?
A protocol is a standardized set of rules that determines how data is transmitted between different devices in a network. It's essentially a common language that allows different devices to communicate with each other, regardless of their underlying architecture or design.

Examples of protocols include:
- HTTP/HTTPS (Web browsing)
- SMTP (Email transfer)
- FTP (File transfer)
- SSH (Secure remote login)

### What is an IP Address?
An IP (Internet Protocol) address is a unique numerical label assigned to each device connected to a computer network. It serves two main functions:
- **Host or network interface identification**
- **Location addressing**

IP addresses come in two versions:
- **IPv4**: Uses a 32-bit address scheme allowing for 4.3 billion unique addresses (e.g., 192.168.1.1)
- **IPv6**: Uses a 128-bit address scheme providing a vastly larger addressing capability (e.g., 2001:0db8:85a3:0000:0000:8a2e:0370:7334)

### What is TCP/IP?
TCP/IP (Transmission Control Protocol/Internet Protocol) is the fundamental communication protocol suite of the Internet. It's a layered framework for organizing communication systems, with each layer responsible for different aspects of data transmission.

The four layers of the TCP/IP model are:
1. **Application Layer**: User-end applications like HTTP, FTP, SMTP
2. **Transport Layer**: Handles end-to-end communication (TCP, UDP)
3. **Internet Layer**: Manages packet routing (IP)
4. **Network Interface Layer**: Physical connection and data framing

TCP is connection-oriented and provides reliable, ordered delivery of data, whereas UDP (User Datagram Protocol) is connectionless and offers faster, but less reliable data transmission.

### What is an Internet Protocol (IP) Port?
An IP port is a 16-bit number (0-65535) that identifies a specific process or service on a server. It works alongside the IP address to direct traffic to the correct application or service on a networked device.

Port numbers are categorized into three ranges:
- **Well-known ports**: 0-1023 (HTTP: 80, HTTPS: 443, FTP: 21, SSH: 22)
- **Registered ports**: 1024-49151 (MySQL: 3306, PostgreSQL: 5432)
- **Dynamic/Private ports**: 49152-65535 (Typically assigned dynamically)

## Servers

### What is a Server?
A server is a physical or virtual computer designed to process requests and deliver data to clients over a network. Unlike personal computers, servers typically:
- Run continuously
- Have higher-grade components for reliability
- Operate without direct user interaction (headless)
- Are optimized for specific functions

### Types of Servers
Servers are often categorized by their function:
- **Web servers**: Serve web content (Apache, Nginx)
- **Application servers**: Run application code (Tomcat, Gunicorn)
- **Database servers**: Host databases (MySQL, PostgreSQL)
- **File servers**: Provide file storage and access
- **Mail servers**: Handle email processing
- **DNS servers**: Resolve domain names to IP addresses

### Server Hosting Environments
Servers are typically housed in:

**Data Centers**
- Specialized facilities for hosting server equipment
- Feature controlled environments (temperature, humidity)
- Have redundant power supplies and internet connections
- Provide physical security measures

**Cloud Infrastructure**
- Virtual servers hosted on distributed physical hardware
- Offer scalability and flexibility
- Reduce need for physical maintenance
- Examples: AWS, Google Cloud, Microsoft Azure

## Web Servers

### Web Server vs. Server
- A **server** is the physical or virtual machine that provides resources or services.
- A **web server** is a software application running on a server that delivers web content via HTTP.

### How Web Servers Work
Web servers handle HTTP requests from clients (browsers) and return appropriate HTTP responses, typically in the form of HTML pages, images, or other web content.

The primary functions of a web server include:
- Processing incoming HTTP requests
- Delivering static content (HTML, CSS, images)
- Routing dynamic requests to application servers
- Managing security controls
- Handling concurrent connections

### Popular Web Servers
- **Nginx**: High-performance web server, reverse proxy, and load balancer
- **Apache**: Widely used web server with extensive feature set
- **Microsoft IIS**: Windows-based web server
- **LiteSpeed**: High-performance alternative to Apache

## DNS (Domain Name System)

### DNS Basics
DNS (Domain Name System) translates human-readable domain names (like www.example.com) into machine-readable IP addresses (like 192.168.1.1). It functions as the "phone book" of the Internet.

The DNS resolution process works as follows:
1. User enters a URL in their browser
2. Computer checks its local cache for the IP address
3. If not found, it queries a recursive DNS resolver (usually provided by the ISP)
4. The resolver works through the DNS hierarchy to find the authoritative answer
5. The IP address is returned to the user's computer
6. The browser connects to the web server at that IP address

### Main DNS Record Types

#### A Record (Address Record)
Maps a domain name to an IPv4 address.
```
example.com.    IN    A    192.0.2.1
```

#### CNAME Record (Canonical Name)
Creates an alias from one domain name to another.
```
www.example.com.    IN    CNAME    example.com.
```

#### MX Record (Mail Exchange)
Specifies mail servers responsible for receiving email for the domain.
```
example.com.    IN    MX    10 mail.example.com.
```

#### TXT Record (Text)
Stores arbitrary text information, often used for verification or SPF records.
```
example.com.    IN    TXT    "v=spf1 include:_spf.example.com ~all"
```

### Advanced DNS Concepts

#### NS Record (Name Server)
Delegates a DNS zone to a set of name servers.
```
example.com.    IN    NS    ns1.example.com.
example.com.    IN    NS    ns2.example.com.
```

#### SOA Record (Start of Authority)
Contains administrative information about the DNS zone.
```
example.com.    IN    SOA    ns1.example.com. admin.example.com. (
                                    2022010101 ; serial
                                    7200       ; refresh
                                    3600       ; retry
                                    1209600    ; expire
                                    86400 )    ; minimum TTL
```

#### Round-Robin DNS
A load balancing technique where multiple IP addresses are associated with a single domain name, and the order is rotated in response to DNS queries.

### Root Domain vs. Subdomain

#### Root Domain
- The primary domain registered with a domain registrar
- Cannot contain dots in its name (example.com)
- Represents the highest level in your domain hierarchy

#### Subdomain
- A domain that is part of a larger domain
- Always contains at least one dot (www.example.com, blog.example.com)
- Can be created and managed without additional registration
- The "www" prefix is technically a subdomain of the root domain

## Load Balancers

### Purpose and Function
Load balancers distribute incoming network traffic across multiple servers to ensure:
- No single server bears too much load
- Service availability is maintained even if servers fail
- Overall system responsiveness is improved
- Scaling becomes more manageable

### Load Balancing Algorithms

#### Round Robin
Requests are distributed sequentially to each server in turn. Simple but doesn't account for server load.

#### Least Connections
Directs traffic to the server with the fewest active connections. Better for sessions of varying duration.

#### IP Hash
Uses the client's IP address to determine which server receives the request. Ensures the same client always reaches the same server.

#### Weighted Round Robin
Servers with higher specifications receive more requests based on assigned weights.

#### Least Response Time
Directs traffic to the server with the lowest combination of active connections and response time.

### Active-Active vs. Active-Passive Setup

#### Active-Active
- All servers actively handle traffic simultaneously
- Provides load distribution across all servers
- Maximizes resource utilization
- Requires more complex configuration

#### Active-Passive
- Only the active server(s) handle traffic
- Passive servers are on standby, ready to take over if active servers fail
- Simpler to implement but less efficient resource utilization
- Primarily focused on high availability rather than load distribution

### Popular Load Balancer Solutions
- **HAProxy**: Software-based, open-source
- **Nginx**: Can function as both web server and load balancer
- **F5 BIG-IP**: Enterprise hardware solution
- **AWS Elastic Load Balancing**: Cloud-based solution

## Monitoring

### Purpose of Monitoring
Monitoring is essential for:
- Detecting and diagnosing problems before they affect users
- Analyzing performance trends
- Planning capacity needs
- Ensuring security compliance
- Validating service quality

### Types of Monitoring

#### Application Monitoring
Focuses on the performance and availability of software applications:
- Response times
- Error rates
- Transaction success
- User experience
- Resource utilization

#### Server Monitoring
Tracks the health and performance of physical or virtual servers:
- CPU usage
- Memory utilization
- Disk space
- Network traffic
- Process counts

#### QPS Monitoring (Queries Per Second)
Measures the throughput of a web server by tracking:
- Number of requests processed per second
- Can be monitored via server logs
- Important for capacity planning and performance optimization

### How Monitoring Tools Collect Data

#### Agent-Based Collection
- Software installed on the monitored systems
- Collects detailed information about the system
- Can access low-level metrics
- Requires maintenance and updates

#### Agentless Collection
- External monitoring via APIs or protocols
- Less intrusive but potentially less detailed
- Easier to deploy across many systems
- Lower overhead on monitored systems

#### Log Analysis
- Parsing and analyzing application and system logs
- Provides insights into errors and behaviors
- Can be resource-intensive for high-volume logs

### Popular Monitoring Tools

#### NewRelic
- Comprehensive application performance monitoring
- JavaScript-based browser monitoring
- Real-time analytics and alerting
- Visualizations and dashboards

#### DataDog
- Infrastructure and application monitoring
- Extensive integration ecosystem
- Metric collection and visualization
- Distributed tracing capabilities

#### Uptime Robot
- Basic availability monitoring
- Multiple check locations worldwide
- Simple setup and configuration
- Free tier available

#### Nagios
- Open-source monitoring solution
- Highly customizable
- Extensive plugin ecosystem
- Server and network monitoring

#### WaveFront
- Time-series data analytics
- SQL-like query language
- Anomaly detection
- Advanced mathematical operations on metrics

## Databases

### What is a Database?
A database is an organized collection of structured information stored electronically and accessed via a database management system (DBMS). Databases provide:
- Structured data storage
- Data retrieval mechanisms
- Data integrity enforcement
- Concurrent access handling
- Backup and recovery capabilities

### Types of Databases

#### Relational Databases (RDBMS)
- Store data in tables with predefined relationships
- Use SQL (Structured Query Language)
- Examples: MySQL, PostgreSQL, Oracle, SQL Server

#### NoSQL Databases
- Store data in various formats (documents, key-value pairs, graphs)
- Typically scale horizontally
- Examples: MongoDB, Redis, Cassandra, Neo4j

### Database Clustering

#### Primary-Replica (Master-Slave) Architecture
In this architecture:
- The Primary node handles write operations
- Replica nodes replicate data from the Primary
- Read operations can be distributed across Replicas
- Provides redundancy and improved read performance

**Primary Node Responsibilities**:
- Processes all write operations
- Updates replicas with changes
- Maintains the authoritative data set

**Replica Node Responsibilities**:
- Maintains a copy of the Primary's data
- Can handle read queries to reduce Primary load
- Can be promoted to Primary if the original Primary fails

## System Redundancy

### What is Redundancy?
Redundancy involves duplicating critical components or functions of a system to increase reliability, typically in the form of a backup or fail-safe.

### Purpose of Redundancy
- **Eliminate Single Points of Failure (SPOF)**: Ensures no single component can cause total system failure
- **Enable Maintenance Without Downtime**: Allows updates and repairs without service interruption
- **Improve Performance Under Load**: Distributes workload across multiple components
- **Enhance Disaster Recovery**: Provides backup systems in case of catastrophic failure

### Redundancy Strategies

#### Hardware Redundancy
- Duplicate servers, storage devices, network equipment
- Redundant power supplies and cooling systems
- Multiple data centers in different geographical locations

#### Network Redundancy
- Multiple network connections and providers
- Redundant routers and switches
- Alternative routing paths

#### Data Redundancy
- Regular backups stored in multiple locations
- Database replication (Primary-Replica setups)
- RAID (Redundant Array of Independent Disks) configurations

#### Geographic Redundancy
- Distributing infrastructure across multiple physical locations
- Protection against natural disasters or regional outages
- Often implemented using cloud providers' multiple availability zones

## Security Components

### Firewalls
A firewall is a network security device that monitors and filters incoming and outgoing network traffic based on predetermined security rules.

#### Types of Firewalls
- **Network Firewalls**: Filter traffic between networks
- **Host-based Firewalls**: Protect individual machines
- **Application Firewalls**: Filter traffic for specific applications
- **Next-Generation Firewalls**: Combine traditional firewall capabilities with advanced features

#### Firewall Deployment Strategies
- **Perimeter Firewalls**: Protect the boundary between internal and external networks
- **Internal Firewalls**: Segment different parts of internal networks
- **Cloud Firewalls**: Protect cloud-based resources

### HTTPS and SSL/TLS

#### What is HTTPS?
HTTPS (Hypertext Transfer Protocol Secure) is the secure version of HTTP, encrypting the data exchanged between a user's browser and the website.

#### Benefits of HTTPS
- **Data Encryption**: Protects sensitive information during transmission
- **Data Integrity**: Prevents modification of transferred data
- **Authentication**: Verifies the identity of the website
- **SEO Advantage**: Preferred by search engines
- **User Trust**: Indicates security to visitors

#### SSL Termination
SSL termination refers to the process of decrypting encrypted traffic before passing it to backend servers:
- Often performed at the load balancer level
- Reduces computational load on application servers
- Can create security concerns if internal traffic is unencrypted
- Best practice is to re-encrypt traffic between load balancer and backend servers

## Web Stack Acronyms

### LAMP Stack
A traditional web service stack consisting of:
- **L**inux (Operating System)
- **A**pache (Web Server)
- **M**ySQL (Database)
- **P**HP/Perl/Python (Programming Language)

### SPOF (Single Point of Failure)
A SPOF is any component whose failure would cause the entire system to fail. In web infrastructure, potential SPOFs include:
- Single server setups
- Lone load balancers
- Single database servers
- Single network connections

### QPS (Queries Per Second)
QPS measures the rate of queries or requests handled by a system per second. It's a key performance metric for:
- Web servers
- Database servers
- API endpoints
- Load balancers

### High Availability
High availability refers to systems designed to operate continuously without failure for long periods. Strategies include:
- Redundant components
- Fault tolerance mechanisms
- Automatic failover capabilities
- Load balancing
- Regular maintenance procedures
