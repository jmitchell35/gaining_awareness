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

A protocol is a standardized set of rules that determines how data is transmitted, exchanged, or represented between different devices in a network. Think of protocols as languages that devices use to communicate with each other effectively.

#### How Protocols Work

Protocols define:
1. **Data Format**: The structure of the data being sent
2. **Address System**: How devices are identified in the network
3. **Transmission Process**: How data is sent from one point to another
4. **Error Detection and Correction**: How to handle problems during transmission
5. **Flow Control**: How to manage the rate of data transmission

#### Protocol Layers

Most network communications use multiple protocols working together in layers. The two common models that describe these layers are:

**OSI (Open Systems Interconnection) Model** - 7 layers:
1. Physical Layer (cables, switches)
2. Data Link Layer (MAC addresses, frames)
3. Network Layer (IP addresses, routing)
4. Transport Layer (TCP, UDP, ports)
5. Session Layer (session establishment)
6. Presentation Layer (data formatting, encryption)
7. Application Layer (HTTP, SMTP, FTP)

**TCP/IP Model** - 4 layers:
1. Network Interface Layer (combines OSI layers 1-2)
2. Internet Layer (similar to OSI layer 3)
3. Transport Layer (similar to OSI layer 4)
4. Application Layer (combines OSI layers 5-7)

#### Common Protocols by Function

**Web Browsing**:
- **HTTP (Hypertext Transfer Protocol)**: Transfers web page content
- **HTTPS**: Secure version of HTTP using encryption
- **TLS/SSL**: Provides security for HTTP and other protocols

**Email**:
- **SMTP (Simple Mail Transfer Protocol)**: Sends emails
- **POP3 (Post Office Protocol)**: Retrieves emails (older)
- **IMAP (Internet Message Access Protocol)**: Manages emails on the server (modern)

**File Transfer**:
- **FTP (File Transfer Protocol)**: Uploads and downloads files
- **SFTP (SSH File Transfer Protocol)**: Secure file transfer
- **SMB (Server Message Block)**: File sharing between Windows computers

**Network Operations**:
- **DNS (Domain Name System)**: Translates domain names to IP addresses
- **DHCP (Dynamic Host Configuration Protocol)**: Assigns IP addresses automatically
- **NTP (Network Time Protocol)**: Synchronizes device clocks

**Data Transport**:
- **TCP (Transmission Control Protocol)**: Reliable, connection-oriented
- **UDP (User Datagram Protocol)**: Faster, connectionless, less reliable

**Secure Communication**:
- **SSH (Secure Shell)**: Encrypted remote administration
- **IPsec (Internet Protocol Security)**: Secures IP communications

#### Protocol Example: HTTP Request/Response

Let's see how HTTP protocol works in practice:

**HTTP Request from Browser**:
```
GET /index.html HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0
Accept: text/html
```

**HTTP Response from Server**:
```
HTTP/1.1 200 OK
Date: Tue, 13 May 2025 12:00:00 GMT
Content-Type: text/html
Content-Length: 1234

<!DOCTYPE html>
<html>
<head>
    <title>Example Website</title>
</head>
<body>
    <h1>Hello, World!</h1>
</body>
</html>
```

This exchange follows specific HTTP protocol rules that both the browser and server understand, enabling them to communicate effectively.

### What is an IP Address?
An IP (Internet Protocol) address is a unique numerical label assigned to each device connected to a computer network that uses the Internet Protocol for communication. It serves two main functions:

1. **Host or Network Interface Identification**: Works like a home address for devices
2. **Location Addressing**: Enables routing of data packets to the right destination

#### IP Address Formats

**IPv4 (Internet Protocol version 4)**:
- Uses a 32-bit address scheme allowing for approximately 4.3 billion unique addresses
- Written as four numbers separated by periods (dots)
- Each number ranges from 0 to 255
- Example: `192.168.1.1`
- Format: `[0-255].[0-255].[0-255].[0-255]`

**IPv6 (Internet Protocol version 6)**:
- Uses a 128-bit address scheme providing a vastly larger addressing capability
- Written as eight groups of four hexadecimal digits separated by colons
- Example: `2001:0db8:85a3:0000:0000:8a2e:0370:7334`
- Can be abbreviated by removing leading zeros and replacing consecutive sections of zeros with `::` (but only once)
- Abbreviated example: `2001:db8:85a3::8a2e:370:7334`

#### Types of IP Addresses

**Public IP Addresses**:
- Assigned by Internet Service Providers (ISPs)
- Unique across the entire internet
- Allows direct access from the internet
- Two subtypes:
  - **Static IP**: Remains constant (used for servers that need consistent addressing)
  - **Dynamic IP**: Changes periodically (typical for home internet connections)

**Private IP Addresses**:
- Used within private networks (homes, offices)
- Not routable on the public internet
- Reserved ranges:
  - `10.0.0.0` to `10.255.255.255` (10.0.0.0/8)
  - `172.16.0.0` to `172.31.255.255` (172.16.0.0/12)
  - `192.168.0.0` to `192.168.255.255` (192.168.0.0/16)

**Loopback Address**:
- `127.0.0.1` (IPv4) or `::1` (IPv6)
- Used to refer to the device itself
- Also called "localhost"

#### How IP Addresses Work in Web Infrastructure

1. **Client-Server Communication**:
   - Your device (client) has an IP address
   - The website server also has an IP address
   - Data packets travel between these addresses using routing

2. **Domain Name Resolution**:
   - Humans use domain names (www.example.com)
   - DNS servers translate domain names to IP addresses
   - Your browser connects to the IP address, not the domain name directly

3. **Network Address Translation (NAT)**:
   - Allows multiple devices on a private network to share one public IP
   - Your home router typically performs NAT
   - Internal devices use private IPs, but appear to the internet using the router's public IP

### What is TCP/IP?

TCP/IP (Transmission Control Protocol/Internet Protocol) is the fundamental suite of protocols that enables communication over the Internet. It's not a single protocol but a collection of protocols that work together, with TCP and IP being the two primary components.

#### The TCP/IP Model Layers

The TCP/IP model organizes network communications into four distinct layers:

1. **Network Interface Layer** (Also called Link or Network Access Layer)
   - Handles physical connection to the network
   - Manages hardware addressing (MAC addresses)
   - Converts data into signals for transmission
   - Examples: Ethernet, Wi-Fi, PPP

2. **Internet Layer**
   - Manages logical device addressing (IP addresses)
   - Routes packets across networks
   - Handles fragmentation and reassembly of data
   - Examples: IP (IPv4, IPv6), ICMP, ARP

3. **Transport Layer**
   - Manages end-to-end communication
   - Ensures data reliability (or speed with UDP)
   - Handles flow control and error recovery
   - Examples: TCP, UDP

4. **Application Layer**
   - Provides network services to applications
   - Handles application-specific protocols
   - Formats data for user interaction
   - Examples: HTTP, FTP, SMTP, DNS

#### TCP vs. UDP: The Two Main Transport Protocols

**TCP (Transmission Control Protocol)**:
- **Connection-oriented**: Establishes a connection before data transfer
- **Reliable**: Ensures data arrives complete and in order
- **Flow control**: Prevents overwhelming the receiver
- **Error detection**: Identifies and retransmits lost packets
- **Handshaking**: Three-way handshake to establish connection
- **Use cases**: Web browsing, email, file transfers, any application where accuracy is critical

**TCP Three-Way Handshake Process**:
1. Client sends SYN (synchronize) packet to server
2. Server responds with SYN-ACK (synchronize-acknowledge)
3. Client sends ACK (acknowledge), establishing the connection

**UDP (User Datagram Protocol)**:
- **Connectionless**: No formal connection establishment
- **Faster**: Lower overhead due to fewer features
- **No guarantees**: Packets may arrive out of order or be lost
- **No congestion control**: Sends at a consistent rate
- **Use cases**: Live streaming, online gaming, VoIP, situations where speed is more important than perfect reliability

#### How Data Moves Through TCP/IP

Let's follow how data is processed when you request a web page:

1. **Application Layer**:
   - You enter "www.example.com" in a browser
   - HTTP protocol formats the request

2. **Transport Layer**:
   - TCP establishes a connection with the server
   - Breaks the data into manageable packets
   - Adds TCP header with port numbers (your browser might use port 49152, destination uses port 80 for HTTP)

3. **Internet Layer**:
   - IP adds source and destination IP addresses
   - Determines the best route for the packets
   - Adds IP header to each packet

4. **Network Interface Layer**:
   - Converts the packets to electrical, radio, or optical signals
   - Transmits the signals over the physical medium (cable, fiber, wireless)

5. **The receiving server processes this data in reverse**:
   - Network Interface Layer receives the signals
   - Internet Layer checks the destination IP
   - Transport Layer reassembles packets and checks integrity
   - Application Layer (web server) interprets the HTTP request

#### TCP/IP in Web Infrastructure

In a web server environment, TCP/IP enables:
- Communication between users and web servers
- Data exchange between web servers and application servers
- Communication between application servers and databases
- Load balancer to server communications

Understanding TCP/IP is fundamental to troubleshooting network issues, configuring servers, and designing robust web infrastructure.

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

#### DNS Hierarchy
The DNS system is organized as a hierarchical tree structure:

1. **Root DNS Servers**: At the top of the hierarchy, these servers know information about the top-level domain (TLD) servers
2. **TLD Servers**: Manage information for domains with a specific extension (.com, .org, .net, etc.)
3. **Authoritative DNS Servers**: Hold the actual DNS records for specific domains
4. **Recursive DNS Resolvers**: The software that queries the DNS hierarchy on behalf of users

#### DNS Resolution Process Explained
When you type a website address in your browser, here's what happens step-by-step:

1. **User Request**: You enter "www.example.com" in your browser
2. **Local Cache Check**: Your computer first checks if it already knows the IP address from previous visits
3. **Resolver Query**: If not found locally, your computer asks a recursive DNS resolver (typically operated by your ISP or services like Google's 8.8.8.8)
4. **Hierarchical Resolution Process**: The resolver then follows a series of steps:
   - If the resolver doesn't know the answer, it starts at the root servers
   - The root server directs the resolver to the .com TLD server
   - The .com TLD server directs the resolver to the authoritative server for example.com
   - The authoritative server provides the IP address for www.example.com
5. **Authoritative Answer**: The final answer comes from the authoritative nameserver that has been officially delegated responsibility for that domain
6. **Result Returned**: The IP address is returned through the chain back to your computer
7. **Connection Established**: Your browser uses this IP address to connect to the web server

The term "authoritative answer" means the response came from a server that has official authority over that domain's records, rather than from cached or second-hand information.

### Main DNS Record Types

DNS records are stored in zone files on authoritative DNS servers. Each record type serves a specific purpose in the DNS system. Let's break down the common record format first:

```
[domain_name]  [TTL]  [class]  [record_type]  [record_data]
```

Where:
- **domain_name**: The name this record applies to
- **TTL**: Time To Live (how long the record can be cached)
- **class**: Almost always "IN" for Internet
- **record_type**: The type of record (A, CNAME, MX, etc.)
- **record_data**: The data specific to this record type

#### A Record (Address Record)
Maps a domain name to an IPv4 address. This is the most fundamental record type.

```
example.com.    86400    IN    A    192.0.2.1
```

This means: "The domain example.com has the IPv4 address 192.0.2.1, and this information can be cached for 86400 seconds (24 hours)."

#### CNAME Record (Canonical Name)
Creates an alias from one domain name to another. Useful for subdomains pointing to the same IP address.

```
www.example.com.    86400    IN    CNAME    example.com.
```

This means: "www.example.com is an alias for example.com. To find the IP address, look up the address for example.com."

#### MX Record (Mail Exchange)
Specifies mail servers responsible for receiving email for the domain.

```
example.com.    86400    IN    MX    10 mail.example.com.
```

This means: "Email for example.com should be delivered to mail.example.com with a priority of 10 (lower numbers have higher priority)."

#### TXT Record (Text)
Stores arbitrary text information, often used for verification or SPF (Sender Policy Framework) records.

```
example.com.    86400    IN    TXT    "v=spf1 include:_spf.example.com ~all"
```

This means: "This is a text record for example.com containing SPF information that defines which mail servers are authorized to send email on behalf of this domain."

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

To understand the relationship between root domains and subdomains, it's important to first understand how domain names are structured within the DNS hierarchy.

#### Domain Name Structure
A fully qualified domain name (FQDN) reads from most specific (left) to most general (right):

```
[subdomain].[root domain].[top-level domain].
```

For example, in `blog.example.com`:
- `.` (implicit trailing dot): The root of the entire DNS system
- `com`: The top-level domain (TLD)
- `example`: The second-level domain (your root domain)
- `blog`: A subdomain of example.com

#### Root Domain
- The primary domain that you register with a domain registrar
- Cannot contain dots in its own name (e.g., `example.com`, not `my.example.com`)
- Represents the highest level in your domain hierarchy that you control
- Requires payment and registration with a domain registrar (like GoDaddy, Namecheap, etc.)

When you register a domain like `example.com`, you gain control over:
- That exact domain name
- All possible subdomains underneath it

#### Subdomain
- A domain that is part of a larger domain
- Always contains at least one dot within the name (e.g., `www.example.com`, `blog.example.com`)
- Can be created and managed without additional registration because you already control the parent domain
- The "www" prefix is technically a subdomain of the root domain

#### Why This Matters
When configuring DNS, understanding the difference helps you:
1. Create proper DNS records for your domains and subdomains
2. Organize your web content logically
3. Apply different security policies to different subdomains
4. Potentially host different services on different subdomains

#### Common Uses for Subdomains
- `www.example.com`: Main website
- `blog.example.com`: Company blog
- `shop.example.com`: E-commerce section
- `api.example.com`: API endpoints
- `mail.example.com`: Email services

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

Load balancers can be configured in two primary high-availability architectures: Active-Active and Active-Passive. Let's explore the differences in detail:

#### Active-Active Configuration
In an Active-Active setup:
- **All servers handle traffic simultaneously**
- Every server in the pool actively accepts and processes requests
- Traffic is distributed across all available servers based on the chosen load balancing algorithm

**How it works:**
1. The load balancer receives a request
2. Based on the load balancing algorithm, it forwards the request to one of the active servers
3. All servers are operational and handling traffic at the same time
4. If one server fails, the load balancer simply stops routing traffic to it while the other servers continue to work

**Advantages:**
- Maximizes resource utilization (all servers are working)
- Higher total throughput capacity
- Automatically distributes load
- Graceful handling of traffic spikes

**Disadvantages:**
- Requires more complex session persistence management
- All servers must be synchronized if handling stateful applications
- May require more total resources in the system

**Diagram Example:**
```
Client Request → Load Balancer → Server 1 (Active)
                              → Server 2 (Active)
                              → Server 3 (Active)
```

#### Active-Passive Configuration
In an Active-Passive setup:
- Only the "active" server(s) handle traffic
- "Passive" servers remain on standby, not processing requests
- Passive servers only become active if the primary active server fails

**How it works:**
1. The load balancer routes all traffic to the active server
2. The passive server constantly monitors the active server
3. If the active server fails, a "failover" occurs
4. The passive server assumes the active role and begins handling traffic

**Advantages:**
- Simpler to implement and manage
- No need for complex data synchronization (the passive server is a direct copy)
- Clearer troubleshooting (you know exactly which server handled what)
- Good for applications that cannot run in parallel

**Disadvantages:**
- Resource inefficiency (passive servers are essentially idle)
- Brief downtime during failover
- Under-utilization of available computing power

**Diagram Example:**
```
Client Request → Load Balancer → Server 1 (Active)
                              → Server 2 (Passive/Standby - only activated if Server 1 fails)
```

#### Which Setup to Choose?

**Choose Active-Active when:**
- Maximum performance and resource utilization are priorities
- Your application is stateless or can easily share state
- You need to handle variable loads efficiently

**Choose Active-Passive when:**
- Simplicity of configuration is more important than maximum utilization
- Your application has complex state that's difficult to synchronize
- Licensing costs are based on active server instances
- You prioritize failover reliability over performance

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
Database clustering is a method for distributing database workload across multiple servers. The Primary-Replica (formerly called Master-Slave) architecture is one of the most common approaches.

**How Primary-Replica Works - Step by Step:**

1. **Setup Structure**: 
   - One database server is designated as the Primary (Master)
   - One or more database servers are designated as Replicas (Slaves)
   - All servers contain the same database structure

2. **Write Operations**:
   - All write operations (INSERT, UPDATE, DELETE) go to the Primary server only
   - The Primary server records these changes in a "binary log"

3. **Replication Process**:
   - Each Replica server has a "replica thread" that connects to the Primary
   - The Replica requests new binary log events from the Primary
   - The Primary sends the binary logs to the Replica
   - The Replica applies these changes to its own database copy

4. **Read Operations**:
   - Read queries can be directed to either the Primary or any Replica
   - Typically, a load balancer distributes read queries among Replicas
   - This distribution reduces the load on the Primary server

5. **Failover Mechanism**:
   - If the Primary server fails, a Replica can be promoted to become the new Primary
   - This process can be manual or automatic depending on the configuration

**Roles and Responsibilities in Detail:**

**Primary Node**:
- Processes and authorizes all database modifications
- Maintains the transaction logs (binary logs)
- Distributes updates to all Replicas
- Serves as the authoritative source of data
- Handles write operations that change the database state

**Replica Node**:
- Maintains a continuously updated copy of the Primary's data
- Typically serves read-only queries to reduce Primary workload
- Provides redundancy in case the Primary fails
- Can be used for backups without affecting the Primary
- Can be promoted to Primary role if the current Primary fails

**Types of Replication:**

1. **Synchronous Replication**:
   - The Primary waits for at least one Replica to confirm changes before considering a transaction complete
   - Provides stronger data consistency but may impact performance

2. **Asynchronous Replication**:
   - The Primary doesn't wait for Replica confirmation
   - Better performance but may lose some data if the Primary fails before replication completes

**Practical Example:**

In a web application:
- When a user creates an account (WRITE operation), the request goes to the Primary database
- When users browse products (READ operations), these requests can be distributed among multiple Replica databases
- If the Primary database server crashes, a Replica can be promoted to Primary to maintain service

This architecture creates a robust system that provides both performance improvement (through distributed reads) and high availability (through redundant data copies).

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
HTTPS (Hypertext Transfer Protocol Secure) is the secure version of HTTP, the protocol over which data is sent between your browser and the website you're connected to. The 'S' at the end of HTTPS stands for 'Secure'.

#### How HTTPS Works - Step by Step
HTTPS secures connections through a mechanism called SSL/TLS (Secure Sockets Layer/Transport Layer Security). Here's how it works:

1. **Initial Connection**: When you navigate to a website using HTTPS, your browser first connects to the web server.

2. **SSL/TLS Handshake**:
   - Your browser requests that the server identify itself
   - The server sends its SSL/TLS certificate, which contains its public key
   - Your browser verifies the certificate with a Certificate Authority (CA)
   - If trusted, your browser creates a symmetric session key
   - This session key is encrypted with the server's public key so only the server can decrypt it
   - The encrypted session key is sent to the server

3. **Secure Communication**:
   - The server decrypts the session key using its private key
   - Now both your browser and the server have the same session key
   - All future communication is encrypted using this session key
   - Anyone intercepting the traffic can only see encrypted data

#### Components of HTTPS Security

1. **Encryption**: Converts data into a code to prevent unauthorized access
   - **Symmetric Encryption**: Same key used to encrypt and decrypt (used for the actual data)
   - **Asymmetric Encryption**: Different keys for encryption and decryption (used during the handshake)

2. **Authentication**: Verifies the identity of the website you're connecting to
   - SSL/TLS certificates are issued by trusted Certificate Authorities
   - These certificates confirm that the website is who it claims to be

3. **Data Integrity**: Ensures data isn't tampered with during transmission
   - Each message includes a message authentication code (MAC)
   - Any change to the data would produce a different MAC

#### SSL/TLS Certificates Explained
An SSL/TLS certificate contains:
- The domain name it was issued for
- Which organization it was issued to
- Which Certificate Authority issued it
- The Certificate Authority's digital signature
- Issue and expiration dates
- The public key (the private key is kept secret by the server)

#### Benefits of HTTPS
- **Data Encryption**: Protects sensitive information like passwords and credit card numbers
- **Data Integrity**: Prevents modification of transferred data
- **Authentication**: Verifies the identity of the website
- **SEO Advantage**: Search engines give preference to HTTPS websites
- **User Trust**: Browsers show a padlock icon for secure connections

#### SSL Termination
SSL termination is the process of decrypting encrypted traffic at the load balancer level before passing it to backend servers.

**How SSL Termination Works**:
1. Encrypted HTTPS traffic arrives at the load balancer
2. The load balancer decrypts the traffic using its SSL certificate and private key
3. The decrypted traffic is then sent to the appropriate backend server
4. The backend server processes the request and sends a response back to the load balancer
5. The load balancer encrypts the response and sends it back to the client

**Advantages of SSL Termination**:
- Reduces computational load on backend servers
- Centralizes SSL certificate management
- Allows load balancers to make routing decisions based on the decrypted content

**Security Concerns with SSL Termination**:
- If internal traffic between the load balancer and backend servers is unencrypted, it creates a security vulnerability
- Within a data center, unencrypted traffic could potentially be intercepted
- Best practice is to re-encrypt traffic between the load balancer and backend servers (SSL bridging)

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
