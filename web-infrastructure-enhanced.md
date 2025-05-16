# Web Infrastructure Design Documentation

This comprehensive guide covers the fundamental concepts of web infrastructure, from networking basics to advanced configurations for high availability, security, and monitoring.

## Table of Contents
- [Network Basics](#network-basics)
- [Complete Web Request Process](#complete-web-request-process)
- [Servers](#servers)
- [Web Servers](#web-servers)
- [DNS (Domain Name System)](#dns-domain-name-system)
- [Load Balancers](#load-balancers)
  - [Purpose and Function](#purpose-and-function)
  - [Load Balancing Algorithms](#load-balancing-algorithms)
  - [Active-Active vs. Active-Passive Setup](#active-active-vs-active-passive-setup)
  - [Health Checks and Monitoring](#health-checks-and-monitoring)
  - [Split-Brain Prevention](#split-brain-prevention)
  - [Traffic Management](#traffic-management)
  - [Session Persistence](#session-persistence)
  - [Load Balancer Redundancy](#load-balancer-redundancy)
  - [Popular Load Balancer Solutions](#popular-load-balancer-solutions)
- [Monitoring](#monitoring)
- [Databases](#databases)
- [System Redundancy](#system-redundancy)
  - [What is Redundancy?](#what-is-redundancy)
  - [Purpose of Redundancy](#purpose-of-redundancy)
  - [Redundancy Strategies](#redundancy-strategies)
  - [High Availability Architectures](#high-availability-architectures)
  - [Failure Detection and Recovery](#failure-detection-and-recovery)
  - [Distributed Consensus Systems](#distributed-consensus-systems)
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
- **HTTPS (Hypertext Transfer Protocol Secure)**: Secure version of HTTP using encryption
- **TLS/SSL (Transport Layer Security/Secure Sockets Layer)**: Provides security for HTTP and other protocols

**Email**:
- **SMTP (Simple Mail Transfer Protocol)**: Sends emails
- **POP3 (Post Office Protocol version 3)**: Retrieves emails (older)
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

#### Why TCP Uses a Three-Way Handshake
The TCP three-way handshake is one of the foundational mechanisms of internet reliability. It uses exactly three steps for several important technical reasons:

**The Three Steps in Detail**:
1. **SYN (Synchronize)**: Client → Server
   * Client sends a packet with SYN flag set
   * Includes initial sequence number (ISN) X chosen by client
   * "I want to talk with you and my sequence numbers will start at X"
2. **SYN-ACK (Synchronize-Acknowledge)**: Server → Client
   * Server acknowledges client's sequence number (X+1)
   * Server includes its own initial sequence number Y
   * "I received your request with sequence X, and I'll respond with sequence Y"
3. **ACK (Acknowledge)**: Client → Server
   * Client acknowledges server's sequence number (Y+1)
   * "I received your response with sequence Y, connection established"

**Why Three Messages Are Necessary**:
A three-way handshake is the **minimum required** to establish a reliable bidirectional connection because:
1. **Bidirectional Verification**:
   * TCP connections are full-duplex (data flows both ways)
   * Both parties must confirm they can send AND receive
   * Each direction needs its own sequence number initialization
2. **Guarding Against Duplicates**:
   * Two-way wouldn't confirm the client received the server's sequence number
   * Without the third ACK, the server can't be sure its parameters were received
   * This prevents duplicate connections and out-of-order packets
3. **State Synchronization**:
   * The three steps ensure both sides are in sync before data transmission
   * Prevents "half-open" connections where one side thinks it's connected but the other doesn't

**Why Not Two or Four Steps?**
* **Two steps would be insufficient**: The client would have no way to acknowledge the server's sequence number
* **Four steps would be redundant**: Once both sides have acknowledged each other's sequence numbers, the connection is established and additional handshaking would just add unnecessary delay

This elegant three-step process ensures both reliability and efficiency in establishing TCP connections.

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

An IP port is a 16-bit number (ranging from 0 to 65535) that identifies a specific process or service on a networked device. While IP addresses identify devices on a network, ports identify specific services or applications running on those devices.

#### How Ports Work

Think of an IP address like a building's street address, and ports like individual apartment numbers within that building. The combination of IP address and port number creates a complete "location" called a socket:

```
Socket = IP Address + Port Number
Example: 192.168.1.1:80
```

When data arrives at a device, the operating system examines the destination port number to determine which application or service should receive the data.

#### Port Categories

Ports are divided into three ranges:

1. **Well-Known Ports (0-1023)**
   - Reserved for common, standardized services
   - Requires administrative privileges to use on most systems
   - Examples:
     - **20/21**: FTP (File Transfer Protocol)
     - **22**: SSH (Secure Shell)
     - **25**: SMTP (Simple Mail Transfer Protocol, for sending email)
     - **53**: DNS (Domain Name System)
     - **80**: HTTP (Hypertext Transfer Protocol)
     - **443**: HTTPS (HTTP Secure)

2. **Registered Ports (1024-49151)**
   - Registered with IANA (Internet Assigned Numbers Authority)
   - Used by specific applications but not as strictly controlled
   - Examples:
     - **1433**: Microsoft SQL Server
     - **3306**: MySQL Database
     - **3389**: Remote Desktop Protocol (RDP)
     - **5432**: PostgreSQL Database
     - **8080**: Alternative HTTP port, often used for web proxies

3. **Dynamic/Private Ports (49152-65535)**
   - Not controlled by any authority
   - Available for temporary use by applications
   - Typically used for outgoing connections or temporary services
   - Example: When your browser connects to a website, it might use port 54321 as its source port

#### Ports in Web Infrastructure

**Web Server Ports**:
- Port 80: Standard for HTTP web traffic
- Port 443: Standard for HTTPS (encrypted) web traffic

**Database Ports**:
- Port 3306: MySQL
- Port 5432: PostgreSQL
- Port 27017: MongoDB

**Application Server Ports**:
- Port 8000/8080: Common for development web servers
- Port 8443: Often used for secure application servers
- Port 9000: Used by many application frameworks

#### How Ports Enable Multiple Services

Ports allow a single server to run multiple services simultaneously. For example, a single server might run:
- A web server on port 80 and 443
- A database on port 3306
- An email server on ports 25, 110, and 143
- SSH access on port 22

Without ports, each service would require a separate IP address.

#### Practical Example: Web Request Flow

When you type "https://www.example.com" in your browser:

1. Your browser determines this is an HTTPS request that uses port 443
2. It connects to the server's IP address on port 443
3. The server's operating system directs the connection to the web server application listening on port 443
4. The web server processes the request and sends a response
5. The response returns to your browser on the dynamic port it opened for this connection

#### Security and Ports

Ports play a crucial role in network security:
- Firewalls often block specific ports to prevent unwanted access
- Security scans check for unnecessarily open ports
- Changing default ports can help prevent automated attacks

Understanding ports is essential for configuring server applications, setting up firewalls, and troubleshooting network connectivity issues.

## Complete Web Request Process

Before diving into specific components, it's important to understand the complete process that occurs when a user accesses a website. This sequential process involves multiple steps and protocols working together:

### 1. DNS Resolution
* **Purpose**: Translate human-readable domain names into machine-readable IP addresses
* **Protocol**: DNS (Domain Name System) on port 53, primarily using UDP
* **Process**: Multiple cache checks and potentially recursive lookups as detailed in the DNS section
* **Output**: IP address of the requested web server
* **Important Note**: No actual website content is transferred during this phase
* **Timing**: Typically 1-400ms depending on cache status

### 2. TCP Connection Establishment
* **Purpose**: Create reliable communication channel between client and server
* **Protocol**: TCP (Transmission Control Protocol)
* **Process**: Three-way handshake (SYN, SYN-ACK, ACK)
* **For HTTPS sites**: Additional TLS handshake for secure connection:
   * Certificate exchange and validation
   * Encryption method negotiation
   * Symmetric key exchange
* **Timing**: 50-200ms (more for HTTPS due to TLS handshake)

### 3. HTTP Request
* **Purpose**: Client specifies what resource it wants from the server
* **Protocol**: HTTP or HTTPS on port 80 or 443
* **Components**:
   * Request method (GET, POST, etc.)
   * Path to resource (/index.html)
   * HTTP version
   * Headers (User-Agent, Accept, Cookie, etc.)
   * Optional body (for POST requests)
* **Example**:

```
GET /index.html HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0
Accept: text/html
```

* **Timing**: Typically 10-100ms to send request

### 4. Server-Side Processing
* **Purpose**: Generate or retrieve requested content
* **Components potentially involved**:
   * Web server (Nginx, Apache)
   * Application server
   * Database queries
   * Caching layers
   * Microservices
* **Timing**: Highly variable (10ms-10s) depending on application complexity

### 5. HTTP Response
* **Purpose**: Deliver requested content back to client
* **Components**:
   * Status code (200 OK, 404 Not Found, etc.)
   * Response headers
   * Response body (HTML, JSON, images, etc.)
* **Example**:

```
HTTP/1.1 200 OK
Date: Wed, 14 May 2025 12:00:00 GMT
Content-Type: text/html
Content-Length: 1234

<!DOCTYPE html>
<html>...
```

* **Timing**: Depends on content size and connection speed

### 6. Content Rendering
* **Purpose**: Display content to user
* **Process**:
   * Parse HTML
   * Load and execute CSS
   * Execute JavaScript
   * Render page visually
   * Make additional requests for embedded resources (images, scripts, etc.)
* **Timing**: 100ms-several seconds depending on page complexity

### Key Insights
* The entire process is sequential - each step must complete before the next can begin
* DNS resolution is a prerequisite lookup service that occurs before any web content is transferred
* Multiple network round-trips are required even for simple pages
* Caching at various levels (DNS, TCP connections, HTTP responses) significantly improves performance
* Modern websites typically trigger dozens of these complete request cycles for a single page load

Understanding this complete cycle helps clarify how each component in web infrastructure contributes to the overall user experience.

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
- Examples: AWS (Amazon Web Services), Google Cloud, Microsoft Azure

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
- **Nginx** (pronounced "engine-x"): High-performance web server, reverse proxy, and load balancer
- **Apache HTTP Server**: Widely used web server with extensive feature set
- **Microsoft IIS** (Internet Information Services): Windows-based web server
- **LiteSpeed**: High-performance alternative to Apache

## DNS (Domain Name System)

### DNS Basics
DNS (Domain Name System) translates human-readable domain names (like www.example.com) into machine-readable IP addresses (like 192.168.1.1). It functions as the "phone book" of the Internet.

#### DNS Hierarchy
The DNS system is organized as a hierarchical tree structure:

1. **Root DNS Servers**: At the top of the hierarchy, these servers know information about the Top-Level Domain (TLD) servers
2. **TLD Servers**: Manage information for domains with a specific extension (.com, .org, .net, etc.)
3. **Authoritative DNS Servers**: Hold the actual DNS records for specific domains
4. **Recursive DNS Resolvers**: The software that queries the DNS hierarchy on behalf of users

#### DNS Resolution Process Explained
When you type a website address in your browser, here's what happens step-by-step:

1. **User Request**: You enter "www.example.com" in your browser
2. **Local Cache Check**: Your computer first checks if it already knows the IP address from previous visits
3. **Resolver Query**: If not found locally, your computer asks a recursive DNS resolver (typically operated by your ISP or services like Google's 8.8.8.8)
4. **Hierarchical Resolution Process**: The resolver then follows a series of steps through the DNS hierarchy:
   - If the resolver doesn't know the answer, it starts by querying the root servers
   - The root server doesn't know the specific answer but directs the resolver to the appropriate TLD server (e.g., .com TLD server)
   - The .com TLD server doesn't know the specific answer either but directs the resolver to the authoritative nameservers for example.com
   - The authoritative nameserver for example.com provides the actual IP address for www.example.com
5. **Authoritative Answer**: The final answer comes from the authoritative nameserver that has been officially delegated responsibility for that specific domain (not from root or TLD servers)
6. **Result Returned**: The IP address is returned through the chain back to your computer
7. **Connection Established**: Your browser uses this IP address to connect to the web server

The term "authoritative answer" means the response came from a server that has been officially designated as the authority for that specific domain's records, rather than from cached or second-hand information. Root servers and TLD servers are authoritative only for their respective zones, not for specific domain names.

### Complete DNS Resolution Flowchart

For a more detailed understanding, here's a comprehensive view of the complete DNS resolution process including all caching layers:

1. **Browser Request Initiated**
   - User enters domain name (www.example.com) in browser
   
2. **Browser DNS Cache Check**
   - Browser checks its internal DNS cache
   - If found and not expired, skip to step 8
   - Each browser maintains its own separate cache

3. **Operating System Cache Check**
   - OS checks its system-wide DNS cache
   - If found and not expired, skip to step 8
   - Used by all applications on the device
   - Windows: View with `ipconfig /displaydns`

4. **Router/Gateway Cache Check**
   - Request goes to local network router/gateway (often ISP-provided)
   - Router checks its local DNS cache
   - If found and not expired, skip to step 8
   - Benefits all devices on the local network

5. **Recursive Resolver Selection**
   - Based on network configuration, either:
     - ISP's DNS resolvers (default)
     - Public DNS (if manually configured)
     - Corporate DNS (in business environments)

6. **Recursive Resolver Processing**
   - Resolver checks for full answer (A/AAAA records) in its cache
   - If found and not expired, skip to step 7
   - If not found, resolver initiates hierarchical resolution:
     - **NS Record (NSSET) Caching**: Check if nameservers for this domain are cached
       - If NS records are cached, go directly to authoritative servers
     - **TLD/ccTLD Server Caching**: Check if TLD server info is cached
       - If TLD server info is cached, skip root server query
     - **Root Hints File**: If no cached information is available, use root hints file 
       - Root hints contain addresses of root servers
       - Not a true cache but a configuration file present in all resolvers
     - Navigate down through hierarchy until reaching authoritative servers
     - Get the final answer from authoritative nameservers

7. **Answer Caching**
   - Recursive resolver caches the answer according to TTL
   - Router may cache the response
   - Operating system caches the response
   - Browser caches the response

8. **Answer Returned to Application**
   - IP address provided to browser
   - Browser establishes connection to web server using this IP

### DNS Caching Hierarchy

DNS uses a multi-layered caching system to improve efficiency. From fastest to slowest:

#### 1. Browser DNS Cache
- Location: Within each web browser's memory
- Scope: Only used by that specific browser
- Speed: Fastest (1-5ms)
- TTL: Usually shortest (minutes to hours)
- Example: Chrome cache is separate from Firefox

#### 2. Operating System DNS Cache
- Location: OS level, managed by system resolver
- Scope: Available to all applications on the device
- Speed: Very fast (5-10ms)
- Viewing: 
  - Windows: `ipconfig /displaydns`
  - Linux: Varies by distribution
  - macOS: System resolver services

#### 3. Router/Gateway DNS Cache
- Location: Home/office router or ISP-provided gateway
- Scope: All devices on the local network
- Speed: Fast (10-20ms)
- Benefit: Shared resource for all local devices
- Often present in ISP-provided "boxes" (Livebox, Freebox, etc.)

#### 4. Recursive Resolver Caching Layers

**Full Answer Caching (A/AAAA records)**
- Has complete IP address information cached
- No external queries needed
- Fastest external resolution possibility (~20-50ms)
- Subject to TTL values set by authoritative servers

**NS Record (NSSET) Caching**
- Resolver has cached which nameservers are authoritative for a specific domain
- Skips root and TLD servers
- Goes directly to authoritative nameservers
- Still requires external query (~40-100ms)

**TLD/ccTLD Server Caching**
- Resolver knows which servers handle specific TLDs or country-code TLDs (.com, .org, .fr, .uk, etc.)
- Skips root servers
- Queries TLD server, then authoritative servers
- Requires two external queries (~60-150ms)
- Separate caches for generic TLDs and country-code TLDs

**Root Hints File**
- Not truly a cache, but a configuration file that bootstraps the DNS system
- Contains addresses of the 13 logical root servers
- Present in every resolver implementation
- Updated very rarely (only when root server information changes)
- Used when other cached information is unavailable 
- Enables full recursive resolution (~100-300ms)

### DNS Resolution Time Comparison

The caching hierarchy creates significant performance differences:

**Full Recursive Lookup (Worst Case)**
- Client to Local Resolver: ~5-20ms
- Resolver to Root Server: ~20-100ms
- Resolver to TLD Server: ~20-100ms
- Resolver to Authoritative Server: ~20-200ms
- Total time range: ~65-420ms

**With NS Record Caching**
- Client to Local Resolver: ~5-20ms
- Resolver to Authoritative Server: ~20-200ms
- Total time range: ~25-220ms
- Time savings: ~40-200ms (approximately 50% faster)

**With Full Answer Caching (at Resolver)**
- Client to Local Resolver: ~5-20ms
- Response from resolver cache: ~1-5ms
- Total time range: ~6-25ms
- Time savings: ~60-400ms (approximately 90% faster)

**With Browser/OS Cache (Best Case)**
- Response from local cache: ~1-5ms
- Total time range: ~1-5ms
- Time savings: ~65-415ms (approximately 99% faster)

This multi-layered caching system makes the DNS resolution process extremely efficient despite its distributed hierarchical nature, with each layer progressively reducing query time.

### DNS System Hierarchy: Who Controls What

Understanding who controls each level of the DNS hierarchy helps clarify how the entire system functions as a distributed but coordinated global network.

#### Root DNS Servers

**Who operates them?**
The Root DNS servers are not controlled by a single entity but are managed by 12 independent organizations that collectively operate the 13 logical root server identities (labeled A through M). As of May 2025, there are approximately 1,952 physical server instances distributed globally.

These operators include:
- Verisign (operates two root servers - A and J)
- University of Southern California (ISI)
- Cogent Communications
- University of Maryland
- NASA (National Aeronautics and Space Administration, Ames Research Center)
- Internet Systems Consortium (ISC)
- U.S. Department of Defense
- U.S. Army Research Lab
- Netnod
- RIPE NCC (Réseaux IP Européens Network Coordination Centre)
- ICANN (Internet Corporation for Assigned Names and Numbers, operates L-Root)
- WIDE Project

**Where are they located?**
Contrary to common misconception, the Root DNS system is not 13 physical servers but rather 13 logical identities implemented using anycast addressing. This allows each logical server to exist as hundreds of physical servers globally distributed. The system uses anycast routing to direct requests based on load and proximity, making it highly redundant and improving performance worldwide.

**Do they exist as a single coherent entity?**
No, the system is intentionally distributed and decentralized. While there are only 13 root server IP addresses visible from any single location at any given time, these addresses point to a global network of servers spanning all populated continents. Together, these 13 root identities represent over 1,500 individual servers, each providing identical information from the root zone to DNS resolvers.

**What is their role in the DNS hierarchy?**
Root servers are authoritative only for the root zone itself. They don't provide IP addresses for specific domains but instead direct resolvers to the appropriate TLD servers. They form the critical first step in the DNS resolution process.

#### TLD Servers

**Who operates them?**
TLD servers are operated by various registry operators who have been delegated authority by ICANN:
- Verisign operates .com and .net
- Public Interest Registry operates .org
- AFNIC operates .fr (France)
- Nominet operates .uk (United Kingdom)
- auDA operates .au (Australia)

Each TLD has its own designated registry operator responsible for maintaining the authoritative DNS servers for that specific top-level domain.

**Where are they located?**
Like root servers, TLD servers use anycast technology to distribute servers globally. Each registry operator maintains their own infrastructure with multiple instances around the world for redundancy and performance.

**Do they exist as single coherent entities?**
Each TLD is managed by a specific registry operator (a single responsible entity), but the physical implementation of each TLD's DNS service is distributed across multiple servers and locations.

**What is their role in the DNS hierarchy?**
TLD servers are authoritative only for their specific TLD zone (like .com or .org). They don't provide IP addresses for specific domains but instead direct resolvers to the appropriate authoritative nameservers for a given domain within their TLD.

#### Authoritative DNS Servers

**Who operates them?**
Authoritative DNS servers are operated by:
- Domain owners themselves
- Domain registrars (like GoDaddy, Namecheap)
- Specialized DNS hosting providers (like Cloudflare, AWS Route 53)
- Web hosting companies that include DNS services

**Domain registration connection:**
This directly relates to domain uniqueness and registration. Domain names must be unique within their TLD, and they are registered through domain registrars accredited by the relevant TLD registry. When you register a domain, you specify which authoritative DNS servers will be responsible for your domain's DNS records. The registrar communicates this information to the TLD registry, which then updates its servers to point to your authoritative DNS servers.

**What is their role in the DNS hierarchy?**
Authoritative nameservers are the only servers that provide the actual, authoritative DNS records (like A, CNAME, MX records) for specific domain names. They are the final authority on what IP address corresponds to a given domain name. While root servers and TLD servers are part of the lookup process, only these authoritative nameservers provide the actual answers for specific domains.

#### Recursive DNS Resolvers

**Software implementations:**
There are multiple implementations of recursive DNS resolver software:
- BIND (Berkeley Internet Name Domain) - The most widely used DNS software
- Unbound - A validating, recursive DNS resolver
- PowerDNS Recursor - A high-performance resolver
- Knot Resolver - A modern open-source resolver
- dnsmasq - Lightweight resolver often used in small networks and routers
- Microsoft DNS - Included with Windows Server

Recursive resolvers need a "root hints file" containing the names and IP addresses of root servers to bootstrap the DNS resolution process. For many DNS resolver software, this list comes built into the software.

**Who operates recursive resolvers?**
These resolvers can be:
- Run by ISPs (Internet Service Providers) for their customers
- Operated as public services (like Google's 8.8.8.8, Cloudflare's 1.1.1.1, Quad9's 9.9.9.9)
- Deployed within enterprise networks
- Part of home routers/gateways
- Run directly on end-user devices

The resolvers cache results to improve performance and reduce load on authoritative servers.

### Self-Hosting DNS Servers

While many organizations use managed DNS services provided by registrars or cloud providers, some enterprises choose to host and manage their own authoritative DNS servers. Here's what's involved in self-hosting DNS:

#### DNS Server Software Options

**BIND (Berkeley Internet Name Domain)**:
- Industry standard, open-source DNS server software
- Primarily runs on Linux/Unix systems
- Configuration complexity level: Moderate to high
- Uses text-based configuration files with specific syntax
- Requires understanding of zone files and resource records
- Needs regular security updates and monitoring

**Microsoft DNS**:
- Integrated with Windows Server and Active Directory
- More GUI-driven, less text configuration
- Complexity level: Moderate
- More intuitive for organizations already using Windows infrastructure
- Offers integration with other Microsoft services

**Other Options**:
- PowerDNS: Flexible, scalable with database backends
- Knot DNS: High-performance authoritative server
- NSD (Name Server Daemon): Focused on security and stability

#### Key Considerations for Self-Hosting DNS

**Technical Requirements**:
- Dedicated servers or virtual machines
- Redundant systems for high availability (at least two servers)
- Regular backups of configuration and zone data
- Monitoring systems to alert on issues

**Operational Challenges**:
- Maintaining 24/7 availability (DNS is critical infrastructure)
- Keeping up with security patches and best practices
- Managing zone transfers between primary and secondary servers
- Handling DNSSEC if implemented

**Security Concerns**:
- DNS servers are common attack targets
- Need protection against DNS amplification attacks
- Preventing cache poisoning and other DNS-specific vulnerabilities
- Implementing proper access controls

**When Self-Hosting Makes Sense**:
- Large enterprises with specialized DNS needs
- Organizations with strict control requirements
- Environments with substantial internal DNS requirements
- When integration with internal systems is critical

**When Managed DNS is Preferable**:
- Small to medium organizations with limited IT resources
- When global distribution and high performance are needed
- When DDoS protection is a priority
- To reduce operational overhead

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

```
Request 1 → Server A
Request 2 → Server B
Request 3 → Server C
Request 4 → Server A (cycle repeats)
```

#### Least Connections
Directs traffic to the server with the fewest active connections. Better for sessions of varying duration.

```
Server A: 100 connections
Server B: 50 connections
Server C: 150 connections
→ New request goes to Server B
```

#### IP Hash
Uses the client's IP address to determine which server receives the request. Ensures the same client always reaches the same server.

```
Client IP: 203.0.113.10 → Hash → Server B (consistently)
Client IP: 198.51.100.5 → Hash → Server A (consistently)
```

#### Weighted Round Robin
Servers with higher specifications receive more requests based on assigned weights.

```
Server A: Weight 3
Server B: Weight 2
Server C: Weight 1

Request 1 → Server A
Request 2 → Server A
Request 3 → Server A
Request 4 → Server B
Request 5 → Server B
Request 6 → Server C
Request 7 → Server A (cycle repeats)
```

#### Least Response Time
Directs traffic to the server with the lowest combination of active connections and response time.

```
Server A: 50ms response time, 100 connections
Server B: 30ms response time, 120 connections
Server C: 20ms response time, 150 connections
→ New request evaluated based on formula combining both metrics
```

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
                     ┌─────────────┐
                     │             │
Client Request ─────►│Load Balancer├─────┬─────┬─────┐
                     │             │     │     │     │
                     └─────────────┘     │     │     │
                                         ▼     ▼     ▼
                                   ┌─────────┐ ┌─────────┐ ┌─────────┐
                                   │Server 1 │ │Server 2 │ │Server 3 │
                                   │(Active) │ │(Active) │ │(Active) │
                                   └─────────┘ └─────────┘ └─────────┘
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
                     ┌─────────────┐
                     │             │
Client Request ─────►│Load Balancer├─────┐
                     │             │     │
                     └─────────────┘     │
                           │             ▼
                           │       ┌─────────┐
                           │       │Server 1 │
                           │       │(Active) │
                           │       └─────────┘
                           │             ▲
                           │             │
                           │       Health Checks
                           │             │
                           ▼             │
                     ┌─────────┐         │
                     │Server 2 │─────────┘
                     │(Passive)│
                     └─────────┘
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

### Health Checks and Monitoring

For any high-availability system, effective health checks are critical to maintain service reliability. Health checks are automated tests performed by load balancers to determine if servers are operating correctly.

#### Health Check Types

**TCP Checks**
- **Description**: The simplest form of health check that verifies if a server responds on a specific TCP port
- **Process**: Opens a TCP connection to a specific port, considers server healthy if connection established successfully
- **Best for**: Basic availability monitoring
- **Limitations**: Only confirms TCP connection, not application functionality
- **Example**: Checking if a web server accepts connections on port 80/443

**HTTP/HTTPS Checks**
- **Description**: Requests a specific URL path and validates the response code
- **Process**: Sends HTTP request to specified path (often `/health` or `/status`), checks for expected response code (typically 200 OK)
- **Best for**: Web applications and REST APIs
- **Advantages**: Confirms application layer functionality
- **Example**: `GET /health HTTP/1.1 Host: example.com`

**Custom Script Checks**
- **Description**: Executes custom scripts or commands to verify specific functionality
- **Process**: Runs predefined scripts that test actual application operations
- **Best for**: Complex applications with specific requirements
- **Advantages**: Can verify business logic and detailed functionality
- **Example**: Simulating a database query and verifying the result

**Application-Specific Checks**
- **Description**: Tests tailored to specific applications or services
- **Process**: Performs application-specific protocols or tests
- **Best for**: Specialized services like databases or message queues
- **Example**: Testing if a MySQL server accepts queries

#### Health Check Configuration Parameters

Load balancer health checks are customizable through several key parameters:

| Parameter | Description | Typical Values | Impact |
|-----------|-------------|----------------|--------|
| Interval | How frequently checks are performed | 5-30 seconds | Lower values provide faster detection but increase load |
| Timeout | Maximum time to wait for a response | 2-10 seconds | Should be less than interval |
| Threshold | Failed checks required to mark server as down | 2-5 checks | Higher values prevent false positives |
| Recovery threshold | Successful checks needed before returning traffic | 2-3 checks | Prevents flapping between states |
| Path (for HTTP) | URL endpoint to check | `/health`, `/status` | Should be lightweight and reliable |
| Expected response | Response code or content expected | 200 OK, specific text | Validates correct application behavior |

#### Health Check Process Flow

```
┌────────────────┐     ┌────────────┐     ┌─────────────────┐
│                │     │            │     │                 │
│  Load Balancer │────►│Health Check│────►│Backend Server(s)│
│                │     │            │     │                 │
└────────────────┘     └────────────┘     └─────────────────┘
        │                                          │
        │                                          │
        ▼                                          ▼
┌────────────────┐                        ┌────────────────┐
│Update Server   │◄───────────────────────│ Return Status  │
│Status in Pool  │                        │ Code/Response  │
└────────────────┘                        └────────────────┘
        │
        │
        ▼
┌────────────────────────┐
│Route Traffic Based on  │
│Updated Pool Status     │
└────────────────────────┘
```

#### Real-World Health Check Example (HTTP)

```
# HAProxy Configuration Example
backend web_servers
    balance roundrobin
    option httpchk GET /health HTTP/1.1\r\nHost:\ example.com
    http-check expect status 200
    default-server inter 5s fall 3 rise 2
    server web01 192.168.1.101:80 check
    server web02 192.168.1.102:80 check
```

This configuration:
- Checks servers every 5 seconds (`inter 5s`)
- Marks a server as down after 3 consecutive failures (`fall 3`)
- Requires 2 successful checks to mark a server as healthy again (`rise 2`)
- Expects a 200 OK response from the `/health` endpoint

#### Dual Monitoring Approach

In high-availability setups, two complementary monitoring mechanisms often work together:

1. **Load Balancer Health Checks**
   - Performed by the load balancer to determine where to route traffic
   - Focuses on service availability from a client perspective
   - Primarily concerned with "Can this server handle new requests?"

2. **Server-to-Server Heartbeats**
   - Direct communication between Active and Passive servers
   - Used for role coordination (determining which server should be Active)
   - Typically uses a dedicated communication channel
   - Focuses on determining server status for failover decisions

These two systems create a comprehensive monitoring framework:
- Load balancer determines traffic routing
- Server heartbeats determine Active/Passive roles
- Both systems can trigger failover under appropriate conditions

### Split-Brain Prevention

Split-brain is a critical failure scenario in high-availability systems that occurs when multiple servers believe they should be active simultaneously. This can lead to data corruption, inconsistent behavior, and service disruption.

#### How Split-Brain Scenarios Occur

Split-brain typically happens due to communication failures rather than server malfunctions:

1. **Network Partition**
   - A network failure separates servers that should be communicating
   - Both servers remain operational but cannot reach each other
   - Each side of the partition has an incomplete view of the system

2. **Failed Communication Channels**
   - When heartbeat links fail but servers continue running
   - From each server's perspective, the other appears to be down

**Visual Example of Network Partition:**
```
Before partition:
┌────────────┐         ┌────────────┐
│            │         │            │
│ Server A   ◄────────►│ Server B   │
│ (Active)   │         │ (Passive)  │
│            │         │            │
└────────────┘         └────────────┘

After network partition:
┌────────────┐    X    ┌────────────┐
│            │         │            │
│ Server A   ◄----//---►│ Server B   │
│ (Active)   │         │ (Passive→Active)│
│            │         │            │
└────────────┘         └────────────┘
  (Continues as        (Sees Server A as down,
   Active)              promotes itself to Active)
```

#### Split-Brain Prevention Mechanisms

Several techniques can be employed to prevent split-brain scenarios:

##### 1. Quorum-Based Decision Making

Quorum is a mathematical approach that ensures system consistency by requiring majority agreement before critical decisions:

- **Definition**: A quorum is the minimum number of nodes that must agree for a decision to be valid
- **Formula**: Typically set as (N/2 + 1) where N is the total number of nodes
- **Example with 3 Nodes**: Quorum would be 2 nodes (3/2 + 1 = 2.5, rounded down to 2)

**How Quorum Works in Practice:**
1. For any critical decision (like becoming the Active server), a node must obtain agreement from a quorum of nodes
2. In a network partition, only one side can have a majority
3. The side without a majority remains in a non-decision-making state

**Diagram of Quorum in Action:**
```
3-Node System (Server A, Server B, Arbiter)
Quorum required = 2 nodes

Scenario: Network partition separates Server A from Server B and Arbiter

┌────────────┐    X    ┌────────────┐
│            │         │            │
│ Server A   │         │ Server B   │
│            │         │            │
└────────────┘         └────────────┘
   Isolated              │
   Cannot form           │
   quorum (1 node)       │
                         │
                         ▼
                  ┌────────────┐
                  │  Arbiter   │
                  │            │
                  └────────────┘
                  
Server B + Arbiter = 2 nodes (quorum achieved)
Server B can become Active
Server A remains in non-decision mode
```

This approach is mathematically guaranteed to prevent split-brain because it's impossible for two conflicting decisions to both achieve majority simultaneously.

##### 2. Fencing Mechanisms (STONITH)

STONITH (Shoot The Other Node In The Head) refers to forcibly isolating a problematic node to prevent it from potentially corrupting shared data. Fencing ensures that a node believed to be malfunctioning cannot continue operating.

**Types of Fencing Devices:**

**Power Fencing**
- **IPMI/BMC Controls**: Intelligent Platform Management Interface/Baseboard Management Controller - dedicated hardware on servers that allows out-of-band monitoring and management (including power control) even when the main OS is unresponsive
- **Power Distribution Units (PDUs)**: Network-connected power strips in data centers that allow remote power cycling of individual outlets
- **Managed UPS Systems**: Uninterruptible Power Supplies with network interfaces that can be commanded to cut power to specific circuits

**Storage Fencing**
- **SAN Zoning Changes**: Reconfiguring Storage Area Network access controls to prevent a specific server from accessing shared storage
- **SCSI Reservations**: Low-level protocol mechanism that allows one server to lock out others from accessing a storage device
- **Storage Array Access Controls**: Enterprise storage systems' ability to mask or unmask LUNs (Logical Unit Numbers) from specific servers

**Network Fencing**
- **VLAN Isolation**: Reconfiguring Virtual LANs to place a problematic server on a separate, isolated network segment
- **SDN-based Network Reconfiguration**: Using Software-Defined Networking to programmatically alter network paths, effectively quarantining a server
- **Switch Port Disabling**: Remotely turning off the physical network port that connects a problematic server

**Fencing Process Flow:**
```
┌───────────────┐       ┌──────────────┐       ┌────────────────┐
│Failure        │       │Decision to   │       │Execute Fencing │
│Detection      ├──────►│Fence Node    ├──────►│Command         │
└───────────────┘       └──────────────┘       └────────────────┘
                                                      │
                                                      │
                                                      ▼
┌───────────────┐       ┌──────────────┐       ┌────────────────┐
│Resume Service │       │Verify Fencing│       │Fencing Action  │
│Operation      │◄──────┤Success       │◄──────┤Executed        │
└───────────────┘       └──────────────┘       └────────────────┘
```

##### 3. Distributed Consensus Systems

Distributed consensus systems provide a reliable way to maintain a consistent state across multiple servers. They act as the "source of truth" for critical decisions like which server should be active.

Popular distributed consensus systems include:
- **ZooKeeper**: Apache project originally created by Yahoo
- **etcd**: Lightweight, distributed key-value store developed by CoreOS, used by Kubernetes
- **Consul**: Service mesh solution by HashiCorp with built-in service discovery

**How Consensus Systems Prevent Split-Brain:**
1. Servers register with the consensus system and obtain leases/locks to become Active
2. Only one server can hold the Active role lock at any time
3. Leases expire and must be renewed, ensuring zombie servers don't retain Active status
4. The consensus system itself is highly available and uses quorum for its own decisions

**Example Diagram:**
```
┌────────────┐         ┌────────────┐
│            │         │            │
│ Server A   │         │ Server B   │
│            │         │            │
└────────────┘         └────────────┘
      │                      │
      └──────────┬───────────┘
                 │
                 ▼
        ┌─────────────────┐
        │                 │
        │ etcd/ZooKeeper/ │
        │ Consul Cluster  │
        │                 │
        └─────────────────┘
```

When using a distributed consensus system:
1. Server A attempts to acquire the "active" lock
2. If successful, it becomes the Active server
3. Server B continuously checks for the lock's availability
4. If Server A fails, its lock expires
5. Server B can then acquire the lock and become Active
6. The consensus system ensures only one server holds the lock at any time

### Traffic Management

Beyond simple failover, load balancers use sophisticated traffic management strategies to ensure optimal performance and availability.

#### Gradual Traffic Restoration with Rate Limiting

When a recovered server comes back online after a failure, sending it 100% of traffic immediately can be risky. Gradual traffic restoration protects against overwhelming a recovering server.

**How Gradual Traffic Restoration Works:**

1. **Initial Limited Traffic**
   - The load balancer initially directs only a small percentage (5-10%) of requests to the recovered server
   - The majority of traffic continues to go to proven healthy servers

2. **Monitoring Phase**
   - The system closely monitors key performance metrics:
     - Response times
     - Error rates
     - Resource utilization (CPU, memory, connections)
   - If performance remains stable at the current traffic level, proceed to step 3

3. **Incremental Traffic Increase**
   - Traffic percentage gradually increases according to a predefined schedule
   - Example progression: 10% → 25% → 50% → 75% → 100%
   - Each step typically waits 2-5 minutes before proceeding to allow for proper assessment

4. **Rate Limiting Protection**
   - During this process, rate limiting caps the maximum requests per second
   - Provides an additional safety mechanism against sudden traffic spikes

**Example Configuration (HAProxy):**
```
backend web_servers
    # Define server with initial weight of 10 (out of 100)
    server web01 192.168.1.101:80 check weight 10
    server web02 192.168.1.102:80 check weight 90
    
    # After monitoring, gradually increase weight through runtime API
```

**Benefits of Gradual Restoration:**
- Allows the server to "warm up" (populate caches, establish connections)
- Provides time to detect lingering issues before full traffic exposure
- Minimizes user impact if problems reoccur
- Prevents the "thundering herd" problem (sudden flood of traffic)

#### Health Check Parameters for Effective Monitoring

Properly configured health checks are essential for both detecting failures and validating recovery. Key health check parameters include:

| Parameter | Function | Best Practice |
|-----------|----------|--------------|
| Check Interval | How frequently health checks run | 5-10 seconds for critical services |
| Timeout | Maximum wait time for response | Set to 1/3 of interval (e.g., 2-3s if interval is 8s) |
| Rise Count | Successful checks before marking healthy | 2-3 checks to confirm stability |
| Fall Count | Failed checks before marking unhealthy | 2-3 checks to avoid false negatives |
| Check Type | Protocol used for health verification | HTTP for web apps, TCP for network services |
| Check URL | Endpoint tested for HTTP checks | Dedicated health endpoint that tests critical components |

### Session Persistence

In high-availability setups, maintaining user sessions during failover is a critical consideration for many applications. Several approaches can be used to ensure session continuity.

#### Shared Session Stores

A shared session store keeps session data in an external, highly-available database that all servers can access. This approach ensures that sessions persist even when servers fail.

**How Shared Session Stores Work:**
1. When a user initiates a session, session data is stored in the external database
2. Each request may be routed to any server in the pool
3. The server retrieves session data from the shared store
4. If a server fails, other servers can still access the session data

**Popular Technologies for Shared Session Stores:**
- **Redis**: In-memory data structure store, used for high-performance session storage
- **Memcached**: Distributed memory caching system
- **MongoDB**: Document database that can store complex session objects
- **MySQL/PostgreSQL**: Traditional databases with session tables

**Example Architecture:**
```
  Web Browsers
       │
       ▼
┌─────────────┐
│             │
│Load Balancer│
│             │
└─────────────┘
     │   │
     │   │
     ▼   ▼
┌─────┐ ┌─────┐
│Web  │ │Web  │
│Server│ │Server│
└─────┘ └─────┘
     │   │
     │   │
     ▼   ▼
┌─────────────┐
│             │
│ Redis/Memcached │
│ Session Store │
│             │
└─────────────┘
```

**Code Example (Node.js with Redis):**
```javascript
const session = require('express-session');
const RedisStore = require('connect-redis')(session);
const redis = require('redis');
const client = redis.createClient({
  host: 'redis.example.com',
  port: 6379
});

app.use(session({
  store: new RedisStore({ client }),
  secret: 'your-secret-key',
  resave: false,
  saveUninitialized: false
}));
```

#### Sticky Sessions with Graceful Degradation

Sticky sessions (also called session affinity) ensure that a user's requests are consistently routed to the same server throughout their session. When combined with graceful degradation, this approach can provide a good balance of performance and reliability.

**How Sticky Sessions Work:**
1. The load balancer assigns a user to a specific server, typically using a cookie or IP-based affinity
2. All subsequent requests from that user are directed to the same server
3. If the assigned server fails, the user is reassigned to a new server

**Graceful Degradation Strategies:**
- Store critical session data in persistent storage
- Use browser storage (localStorage) as backup for critical session data
- Implement session recovery mechanisms
- Design user experience to handle session loss gracefully

**Example Load Balancer Configuration (Nginx):**
```
upstream backend {
    ip_hash;  # IP-based session affinity
    server 192.168.1.101:8080;
    server 192.168.1.102:8080;
}
```

**Comparative Analysis:**

| Approach | Advantages | Disadvantages | Best For |
|----------|------------|--------------|----------|
| Shared Session Store | Complete session persistence during failover | Additional infrastructure component | Applications requiring absolute session continuity |
| Sticky Sessions | Better performance, simpler architecture | Sessions lost if assigned server fails | Applications where occasional session loss is acceptable |
| Hybrid Approach | Balance of performance and reliability | More complex implementation | Most production web applications |

The hybrid approach typically uses sticky sessions for performance, with critical data in a shared store as a fallback mechanism.

### Load Balancer Redundancy

Load balancers themselves can be a single point of failure (SPOF) unless properly configured for high availability. Several techniques can be used to ensure load balancer redundancy.

#### Load Balancer HA Pairs

Load balancers are commonly deployed in Active-Passive high-availability pairs, with automated failover between them.

**How Load Balancer HA Pairs Work:**
1. Two load balancer instances are deployed
2. They share a Virtual IP (VIP) address that clients connect to
3. The Active instance handles all traffic and owns the VIP
4. The Passive instance monitors the Active instance
5. If the Active instance fails, the Passive instance takes over the VIP

**Failover Process:**
```
┌────────────────┐        ┌────────────────┐
│                │        │                │
│  Load Balancer │◄──────►│  Load Balancer │
│  A (Active)    │        │  B (Passive)   │
│                │        │                │
└────────────────┘        └────────────────┘
        │                         │
        │ VIP                     │
        │ 192.168.1.100           │
        ▼                         │
┌────────────────┐                │
│                │                │
│    Clients     │                │
│                │                │
└────────────────┘                │
                                  │
                                  │
After failure:                    │
                                  │
┌────────────────┐        ┌────────────────┐
│                │        │                │
│  Load Balancer │        │  Load Balancer │
│  A (Failed)    │        │  B (Active)    │
│                │        │                │
└────────────────┘        └────────────────┘
                                  │
                                  │ VIP
                                  │ 192.168.1.100
                                  ▼
                          ┌────────────────┐
                          │                │
                          │    Clients     │
                          │                │
                          └────────────────┘
```

#### Virtual IP (VIP) Management

Virtual IP address management is the key to transparent load balancer failover. A VIP is a network address that can move between physical devices.

**How VIP Management Works:**
1. The active device owns the VIP and responds to ARP requests for this IP
2. When failover occurs, the new active device sends a "gratuitous ARP" message
3. This message updates network devices' ARP tables to associate the VIP with the new device's MAC address
4. Traffic is redirected to the new active device without client reconfiguration

**Technical Components:**
- **ARP (Address Resolution Protocol)**: Maps IP addresses to MAC addresses in local networks
- **Gratuitous ARP**: Unsolicited ARP message announcing new ownership of an IP address
- **VRRP (Virtual Router Redundancy Protocol)**: Standardized protocol for managing VIP ownership

**Keepalived Software:**
Keepalived is a popular Linux tool that implements VRRP for load balancer failover. It uses priority values (typically 100-254) to determine which device should own the VIP.

**Example Keepalived Configuration:**
```
# Primary Load Balancer
vrrp_instance VI_1 {
    state MASTER
    interface eth0
    virtual_router_id 51
    priority 200
    authentication {
        auth_type PASS
        auth_pass securepass
    }
    virtual_ipaddress {
        192.168.1.100
    }
}

# Backup Load Balancer
vrrp_instance VI_1 {
    state BACKUP
    interface eth0
    virtual_router_id 51
    priority 100
    authentication {
        auth_type PASS
        auth_pass securepass
    }
    virtual_ipaddress {
        192.168.1.100
    }
}
```

#### Multi-Layered Load Balancer Redundancy

Advanced high-availability architectures often use multiple redundancy approaches together:

##### 1. DNS Round-Robin
**How it Works:**
- Multiple A records for the same domain point to different load balancer IPs
- DNS server returns these IPs in rotating order
- If one load balancer fails, clients can connect to alternative IPs
- Modern implementations include health checks to remove failed endpoints

**Example DNS Records:**
```
www.example.com.    IN    A    203.0.113.10
www.example.com.    IN    A    203.0.113.11
```

##### 2. Global Load Balancing
**How it Works:**
- **GeoDNS**: Returns different answers based on client location
- **Anycast Routing**: Same IP address announced from multiple global locations
- If one location fails, global routing automatically directs traffic to next closest

**Global Distribution Methods:**

| Method | Description | Example Technologies |
|--------|-------------|---------------------|
| GeoDNS | DNS-based geographic routing | AWS Route53, Cloudflare DNS |
| Anycast | BGP routing to nearest instance | Cloudflare, Fastly edge networks |
| Multi-region deployment | Independent deployments in multiple regions | AWS Global Accelerator, Azure Traffic Manager |

**Layered Architecture Example:**
```
Global: Anycast or GeoDNS (geographic redundancy)
  ↓
Regional: DNS load balancing across data centers
  ↓
Local: Hardware/software load balancer pairs with VIPs
  ↓
Internal: Application-level load balancing and service discovery
```

#### Load Balancer Split-Brain Prevention

Load balancer pairs need their own split-brain prevention mechanisms to ensure only one device actively handles traffic at any time.

**Methods to Prevent Load Balancer Split-Brain:**

1. **Dedicated Heartbeat Connections**:
   - Multiple redundant network paths between load balancers
   - Typically 2-3 separate network connections:
     - **Direct crossover cable**: Physical link connecting devices
     - **Private VLAN**: Isolated network segment for heartbeats
     - **Alternative network path**: Heartbeats over different network equipment

2. **Multiprotocol Heartbeats**:
   - UDP multicast: Primary protocol (fastest, lowest overhead)
   - TCP unicast: Secondary protocol (more reliable, connection-oriented)
   - Serial console connection: Tertiary/emergency option for complete network failures

3. **Integration with Consensus Systems**:
   - Load balancers can use the same distributed consensus systems as backend servers
   - etcd, Consul, or ZooKeeper can manage load balancer active/passive roles
   - Provides additional validation layer beyond direct heartbeats

### Popular Load Balancer Solutions

#### Hardware Load Balancers

**F5 BIG-IP Devices**
- Enterprise-grade application delivery controllers (ADCs)
- Provide advanced traffic management beyond simple load balancing
- Include specialized hardware for SSL/TLS acceleration
- Support sophisticated traffic rules and application-layer routing
- Feature built-in security functions like WAF (Web Application Firewall)
- Typical deployment: High-availability pairs in enterprise data centers

**Citrix ADC (formerly NetScaler)**
- Enterprise-level application delivery controllers
- Specialized in application acceleration and security
- Features advanced monitoring and analytics
- Available as hardware appliances or virtual editions
- Common in large Citrix environments

#### Software Load Balancers

**HAProxy**
- Open-source software load balancer
- High performance and reliability
- Supports both Layer 4 (TCP) and Layer 7 (HTTP) load balancing
- Extensive configuration options for advanced use cases
- Widely used in production environments
- Common deployment: Active-passive pairs on Linux servers

**Nginx**
- Web server that also functions as a capable load balancer
- Known for high performance and low resource usage
- Excellent for HTTP/HTTPS load balancing
- Supports TCP load balancing in Nginx Plus (commercial version)
- Popular for both small and large-scale deployments

**Envoy**
- Modern, high-performance edge and service proxy
- Designed for cloud-native applications
- Supports advanced traffic management features
- Extensive observability capabilities
- Popular in microservices and Kubernetes environments

#### Cloud-Based Solutions

**AWS Elastic Load Balancing (ELB)**
- Fully managed load balancing service
- Three types:
  - Application Load Balancer (ALB): For HTTP/HTTPS traffic
  - Network Load Balancer (NLB): For TCP/UDP traffic
  - Classic Load Balancer: Legacy option
- Automatically scales with traffic
- Integrated with other AWS services

**Google Cloud Load Balancing**
- Global load balancing with anycast IP
- Supports HTTP(S), TCP/SSL, and UDP traffic
- Auto-scaling and integrated health checks
- No separate management of load balancer instances

**Azure Load Balancer**
- Microsoft's cloud load balancing solution
- Available in two tiers:
  - Basic: For simple load balancing
  - Standard: Advanced features and higher availability
- Complements Azure Application Gateway for HTTP-specific scenarios

#### Comparison Table

| Solution | Type | Protocol Support | Best For | Distinguishing Features |
|----------|------|-----------------|----------|------------------------|
| F5 BIG-IP | Hardware | L4-L7, advanced protocols | Enterprise with complex requirements | Comprehensive feature set, hardware acceleration |
| HAProxy | Software | TCP, HTTP(S) | Cost-effective, general purpose | Excellent performance-to-resource ratio |
| Nginx | Software | HTTP(S), TCP (Plus) | Web traffic, reverse proxy | Dual web server/load balancer capability |
| AWS ELB | Cloud | HTTP(S), TCP, UDP | AWS workloads | No management overhead, auto-scaling |
| Envoy | Software | HTTP(S), TCP, UDP, gRPC | Microservices architectures | Modern design, extensive telemetry |

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
- CPU (Central Processing Unit) usage
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
- Comprehensive APM (Application Performance Monitoring)
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

### High Availability Architectures

High Availability (HA) refers to systems that are designed to operate continuously without failure for long periods. These architectures employ redundancy strategies alongside sophisticated failure detection and recovery mechanisms.

#### Availability Tiers and Uptime

Availability is typically measured in "nines" - the percentage of time a system is operational:

| Availability | Downtime per Year | Tier Level | Common Use Cases |
|--------------|-------------------|------------|------------------|
| 99% (2 nines) | 3.65 days | Basic | Development environments |
| 99.9% (3 nines) | 8.77 hours | Standard | Internal business applications |
| 99.99% (4 nines) | 52.6 minutes | High Availability | Customer-facing services |
| 99.999% (5 nines) | 5.26 minutes | Mission Critical | Payment systems, critical infrastructure |
| 99.9999% (6 nines) | 31.5 seconds | Ultra High Availability | Telecommunications, life-critical systems |

#### N+1 Redundancy

N+1 redundancy is a form of resilience that ensures there is at least one more instance of a component than is needed to handle the load. If N units are required for normal operation, then N+1 units are installed.

**Example:**
If 3 web servers are needed to handle regular traffic (N=3), a system with N+1 redundancy would have 4 web servers. If one server fails, the system can still handle the full load.

```
N+1 Redundancy Example (for N=3):

┌────────────┐ ┌────────────┐ ┌────────────┐ ┌────────────┐
│            │ │            │ │            │ │            │
│  Server 1  │ │  Server 2  │ │  Server 3  │ │  Server 4  │
│            │ │            │ │            │ │            │
└────────────┘ └────────────┘ └────────────┘ └────────────┘
      ▲              ▲              ▲              ▲
      │              │              │              │
      └──────────────┴──────────────┴──────────────┘
                             │
                             ▼
                     ┌────────────────┐
                     │                │
                     │ Load Balancer  │
                     │                │
                     └────────────────┘
```

#### 2N Redundancy (Dual Redundancy)

2N redundancy provides twice the resources needed for normal operation, effectively creating a complete backup system. This approach is used for mission-critical systems where any downtime is unacceptable.

**Example:**
If 3 web servers are needed (N=3), a 2N redundant system would have 6 web servers, often split between two separate environments.

```
2N Redundancy Example:

Primary Site:                      Secondary Site:
┌────────────┐                     ┌────────────┐
│ Web Server │                     │ Web Server │
└────────────┘                     └────────────┘
      ▲                                  ▲
      │                                  │
┌────────────┐                     ┌────────────┐
│ App Server │                     │ App Server │
└────────────┘                     └────────────┘
      ▲                                  ▲
      │                                  │
┌────────────┐                     ┌────────────┐
│  Database  │ ─── Replication ───►│  Database  │
└────────────┘                     └────────────┘
      ▲                                  ▲
      │                                  │
┌────────────┐                     ┌────────────┐
│   Power    │                     │   Power    │
└────────────┘                     └────────────┘
```

### Failure Detection and Recovery

Robust high-availability systems need mechanisms to detect failures quickly and initiate recovery processes automatically.

#### Heartbeat Systems

Heartbeat systems continuously monitor the status of components through regular "I'm alive" messages.

**How Heartbeats Work:**
1. Components send periodic signals (heartbeats) to each other
2. If a heartbeat isn't received within a predefined interval, the component is considered failed
3. Recovery procedures are initiated automatically

**Implementation Approaches:**
- **Direct Heartbeats**: Components communicate directly with each other
- **Centralized Monitoring**: Components report to a monitoring system
- **Distributed Consensus**: Multiple nodes participate in failure detection

**Multiple Heartbeat Channels:**
Critical systems often use multiple independent communication channels for heartbeats to prevent false positives from network issues:

```
Server A ◄───── Primary Network ─────► Server B
    ▲                                      ▲
    │                                      │
    └────── Secondary Network ─────────────┘
    │                                      │
    └────── Serial Connection ─────────────┘
```

#### Network Partitions

Network partitions (or "split-brain scenarios") occur when network failures divide systems that should be communicating. Understanding this failure mode is critical for building reliable systems.

**Causes of Network Partitions:**
- Switch or router failures
- Cable damage
- Misconfigured firewalls
- Routing table corruption
- Network congestion severe enough to cause timeouts

**Example Partition Scenario:**
```
Before partition:
┌─────────┐     ┌─────────┐     ┌─────────┐
│ Server A│─────│ Switch  │─────│ Server B│
└─────────┘     └─────────┘     └─────────┘

After network partition:
┌─────────┐     ┌─────────┐     ┌─────────┐
│ Server A│─────│ Switch  │─ X ─│ Server B│
└─────────┘     └─────────┘     └─────────┘
   Active                          Promotes
                                  to Active
```

In this scenario, both servers might believe they should be active, leading to split-brain conditions that can corrupt data or cause service inconsistencies.

#### Recovery Time Objectives (RTO) and Recovery Point Objectives (RPO)

Two key metrics define the goals of failure recovery:

**Recovery Time Objective (RTO):**
- The maximum acceptable time to restore service after failure
- Measured from the point of failure to the restoration of service
- Influences architecture decisions and recovery mechanisms

**Recovery Point Objective (RPO):**
- The maximum acceptable data loss measured in time
- Defines how recent the data must be when service is restored
- Impacts backup frequency and replication strategies

**RTO/RPO Relationship:**
```
            Failure
               │
               ▼
┌───────────────────────┐           ┌───────────────────────────┐
│                       │           │                           │
└───────────────────────┘           └───────────────────────────┘
   Last Backup               RPO           Service              RTO
   or Replication           ◄───►         Restored            ◄───►
```

| System Type | Typical RTO | Typical RPO | Example Technologies |
|-------------|-------------|-------------|---------------------|
| Mission Critical | Seconds | Near-zero | Active-Active clusters, synchronous replication |
| Business Critical | Minutes | Minutes | Active-Passive with automated failover |
| Important | Hours | Hours | Warm standby systems |
| Non-critical | Days | Day | Regular backups, manual recovery |

### Distributed Consensus Systems

Distributed consensus systems provide a reliable way to maintain a consistent state across multiple servers. These specialized systems help prevent split-brain scenarios and serve as the "source of truth" for critical decisions in distributed environments.

#### Key Distributed Consensus Technologies

**ZooKeeper**
- Developed by Apache (originally at Yahoo)
- Provides distributed synchronization
- Maintains configuration information
- Used for leader election in distributed systems
- Implements consensus using Zab (ZooKeeper Atomic Broadcast) protocol
- Common in Hadoop ecosystems

**etcd**
- Lightweight, distributed key-value store
- Developed by CoreOS team (now part of Red Hat/IBM)
- Uses the Raft consensus algorithm
- Designed for storing critical configuration data
- Core component of Kubernetes
- Provides distributed locking and leader election

**Consul**
- Created by HashiCorp
- Combines service discovery with consensus
- Features built-in health checking
- Provides a distributed key-value store
- Implements the Raft consensus algorithm
- Includes DNS interface for service discovery

#### How Consensus Systems Work

Distributed consensus systems use specialized algorithms to ensure all nodes in a cluster agree on values despite potential failures or network issues.

**Example: The Raft Algorithm (used by etcd and Consul)**

Raft achieves consensus through several mechanisms:

1. **Leader Election**
   - Cluster always has a single leader node
   - Leader handles all client requests
   - Other nodes act as followers
   - If leader fails, a new election is automatically triggered

2. **Log Replication**
   - All changes first written to a log
   - Leader replicates its log to followers
   - Majority agreement required before committing changes
   - Ensures consistency across the system

3. **Safety Constraints**
   - Leaders can only append to logs, never overwrite
   - Logs are checked for consistency during election
   - Nodes only vote for candidates with up-to-date logs

**Consensus Decision Flow:**
```
┌──────────┐     ┌──────────┐     ┌──────────┐     ┌──────────┐
│ Client   │     │ Leader   │     │ Follower │     │ Follower │
└──────────┘     └──────────┘     └──────────┘     └──────────┘
      │                │                │                │
      │    Request     │                │                │
      │───────────────►│                │                │
      │                │                │                │
      │                │ Append to log  │                │
      │                ├───────────────►│                │
      │                │                │                │
      │                │ Append to log  │                │
      │                ├───────────────────────────────►│
      │                │                │                │
      │                │ Acknowledge    │                │
      │                │◄───────────────┤                │
      │                │                │                │
      │                │ Acknowledge    │                │
      │                │◄───────────────────────────────┤
      │                │                │                │
      │                │ Commit locally │                │
      │                ├────────────────┤                │
      │                │                │                │
      │                │ Commit         │                │
      │                ├───────────────►│                │
      │                │                │                │
      │                │ Commit         │                │
      │                ├───────────────────────────────►│
      │                │                │                │
      │    Response    │                │                │
      │◄───────────────┤                │                │
```

#### Preventing Consensus System Failures

Consensus systems themselves must be highly available to prevent them from becoming a single point of failure.

**Redundancy Strategies for Consensus Systems:**

1. **Odd-Numbered Clusters**
   - Always deploy with 3, 5, or 7 nodes (never even numbers)
   - Allows majority decisions even with failures
   - 3 nodes can tolerate 1 failure
   - 5 nodes can tolerate 2 failures
   - 7 nodes can tolerate 3 failures

2. **Geographic Distribution**
   - Spread nodes across failure domains
   - Different racks, data centers, or regions
   - Prevents correlated failures from taking down majority

3. **Independent Network Paths**
   - Ensure nodes have multiple ways to communicate
   - Separate network connections for inter-node traffic
   - Consider dedicated NICs (Network Interface Cards) for consensus traffic

4. **Regular Monitoring and Maintenance**
   - Health checks specific to consensus systems
   - Automatic replacement of failed nodes
   - Regular software updates with rolling deployments

**Example etcd Cluster Configuration:**
```yaml
# Example of highly available etcd configuration
name: etcd-node-1
data-dir: /var/lib/etcd
initial-cluster-state: new
initial-cluster-token: etcd-cluster-1
initial-cluster: etcd-node-1=https://10.0.1.10:2380,etcd-node-2=https://10.0.1.11:2380,etcd-node-3=https://10.0.1.12:2380
listen-peer-urls: https://10.0.1.10:2380
listen-client-urls: https://10.0.1.10:2379,https://127.0.0.1:2379
advertise-client-urls: https://10.0.1.10:2379
```

#### Using Consensus Systems in High-Availability Applications

Distributed consensus systems are not typically used directly by applications but serve as the coordination layer for high-availability architectures:

1. **Service Discovery**
   - Applications register themselves with the consensus system
   - Clients query the consensus system to locate available services
   - Automatically excludes failed instances

2. **Distributed Locking**
   - Prevents multiple instances from performing conflicting operations
   - Applications acquire locks before executing critical sections
   - Locks automatically released if the holder fails

3. **Leader Election**
   - Used to implement Active-Passive systems
   - Only one application instance becomes the leader
   - Other instances monitor the leader's status
   - Consensus system manages leader transitions

**Practical Application Example:**
```
┌────────────┐                ┌────────────┐
│            │                │            │
│Application │◄───────────────►Application │
│Instance 1  │                │Instance 2  │
│            │                │            │
└────────────┘                └────────────┘
      ▲                              ▲
      │                              │
      └──────────────────────────────┘
                     │
                     ▼
            ┌─────────────────┐
            │                 │
            │  etcd Cluster   │
            │                 │
            └─────────────────┘
                     ▲
                     │
            ┌────────────────┐
            │                │
            │ Load Balancer  │
            │                │
            └────────────────┘
```

In this architecture:
- The etcd cluster maintains the "source of truth" about which application instance is active
- The active instance acquires a lock in etcd
- If the active instance fails, it loses its lock
- The passive instance detects the lock release and acquires it
- The load balancer checks etcd to determine where to send traffic

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

### DMZ (Demilitarized Zone)

A DMZ is a segregated network segment that sits between an organization's trusted internal network and the untrusted external network (the internet). It serves as a buffer zone for hosting public-facing services without exposing the internal network.

#### Purpose of a DMZ
- Adds an additional security layer between external users and internal resources
- Contains potential security breaches by isolating public-facing services
- Creates a buffer zone where additional security controls can be implemented
- Allows external access to specific services without exposing the internal network

#### Components Typically Placed in a DMZ
- **Public-facing web servers**
- **Reverse proxies**
- **External-facing DNS servers**
- **Email gateways and spam filters**
- **VPN endpoints**
- **FTP servers**
- **Web Application Firewalls (WAFs)**

#### Typical DMZ Network Architecture
```
Internet → External Firewall → DMZ (Public-facing Services) → Internal Firewall → Internal Network
```

### Reverse Proxies

A reverse proxy is a server that sits between external users and your internal application servers, forwarding client requests to the appropriate backend servers.

#### Role in Security Architecture
- Typically deployed in the DMZ
- Acts as an intermediary between external users and internal application servers
- Provides a single controlled entry point to internal applications
- Hides internal network structure and IP addresses from external users
- Can perform TLS/SSL termination in the DMZ
- Filters and inspects traffic before it reaches internal servers

#### Popular Reverse Proxy Solutions
- **Nginx**: Lightweight, high-performance
- **HAProxy**: Excellent for load balancing
- **Apache with mod_proxy**: Feature-rich option
- **F5 BIG-IP**: Enterprise-grade solution
- **Cloudflare/Akamai Edge**: Cloud-based options

#### Benefits in Web Infrastructure
- **Security**: Hides backend details, filters malicious requests
- **Load balancing**: Distributes traffic among multiple backend servers
- **Caching**: Improves performance by storing copies of resources
- **SSL termination**: Centralizes certificate management
- **Compression**: Reduces bandwidth usage
- **Content filtering**: Controls what content passes through

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
- API (Application Programming Interface) endpoints
- Load balancers

### High Availability
High availability refers to systems designed to operate continuously without failure for long periods. Strategies include:
- Redundant components
- Fault tolerance mechanisms
- Automatic failover capabilities
- Load balancing
- Regular maintenance procedures

## Advanced High-Availability Terms and Concepts

### Network-Related Terminology

#### ARP (Address Resolution Protocol)
ARP is a protocol used to map an IP address to a physical machine address (MAC address) recognized in the local network.

- **Purpose**: Translates IP addresses to MAC addresses within a local area network
- **How it works**: Devices broadcast "Who has IP x.x.x.x?" and the owner responds with its MAC address
- **ARP Table**: A cache maintained by each device mapping IP addresses to MAC addresses
- **Gratuitous ARP**: An unsolicited ARP message announcing a device's MAC address for a specific IP

#### VRRP (Virtual Router Redundancy Protocol)
VRRP is a networking protocol that provides automatic assignment of available IP routers to participating hosts, increasing network reliability.

- **Purpose**: Ensures network gateway availability by allowing multiple routers to share a virtual IP address
- **Operation**: One router acts as "master" and owns the virtual IP; if it fails, a backup router takes over
- **Priority Mechanism**: Each router is assigned a priority value (higher is preferred)
- **Implementation**: Used by tools like Keepalived for load balancer failover

#### NIC (Network Interface Card)
A NIC is a hardware component that connects a computer to a network.

- **Function**: Provides physical interface between computer and network
- **Redundancy**: Systems may have multiple NICs for fault tolerance
- **Bonding/Teaming**: Multiple NICs can be combined for increased bandwidth and reliability
- **Dedicated NICs**: Critical services may use separate physical network interfaces for isolation

### Anycast Routing
Anycast is a network addressing and routing method where data from a single sender is routed to the closest or best destination from a group of potential receivers all identified by the same destination address.

- **How it works**: The same IP address is announced from multiple locations worldwide
- **Benefits**: Automatically routes traffic to the nearest operational instance
- **Used by**: CDNs, DNS services (including root DNS servers), DDoS protection services
- **Compared to GeoDNS**: Anycast works at routing layer, GeoDNS works at DNS layer

### SAN (Storage Area Network)
A SAN is a dedicated network that provides access to consolidated block-level storage.

- **Purpose**: Provides high-performance, reliable access to storage devices
- **Implementations**: Fibre Channel, iSCSI, FCoE (Fibre Channel over Ethernet)
- **SAN Zoning**: Access control mechanism that defines which servers can access which storage devices
- **Use in HA**: Critical for shared storage access in clustered environments

### Service Mesh
A service mesh is a dedicated infrastructure layer for handling service-to-service communication, typically implemented in microservices architectures.

- **Function**: Manages inter-service communication, security, and observability
- **Components**: Usually consists of proxy sidecars alongside each service
- **Benefits**: Adds resilience, traffic control, and security without modifying application code
- **Examples**: Istio, Linkerd, Consul Connect

### Failover vs. Failback
These related terms describe different phases of recovery:

**Failover**:
- The process of switching to a redundant system when the primary system fails
- Typically automated in high-availability systems
- May involve promoting a passive instance to active role

**Failback**:
- The process of returning operation to the original primary system after it has been restored
- Can be automatic or manual depending on configuration
- Often involves data synchronization to catch up the original primary

### Stateful vs. Stateless Applications
The distinction between stateful and stateless applications significantly impacts high-availability design:

**Stateless Applications**:
- Do not store client session information between requests
- Any instance can handle any request
- Easier to scale horizontally
- Examples: Static web servers, RESTful APIs

**Stateful Applications**:
- Maintain client session state
- Require session persistence or state replication
- More complex to scale and make highly available
- Examples: Shopping carts, multi-step workflows

### BCP (Business Continuity Planning)
BCP involves creating systems of prevention and recovery to deal with potential threats to an organization's operations.

- **Focus**: Ensures critical business functions can continue during and after a disaster
- **Components**: Risk assessment, business impact analysis, recovery strategies
- **Relation to HA**: High availability is one technical implementation of BCP
- **Beyond technology**: Includes processes, people, and facilities

## Advanced Redundancy Concepts

### Asynchronous vs. Synchronous Replication

**Synchronous Replication**:
- Data must be written to both primary and replica before confirming success
- Provides stronger consistency guarantees
- Higher latency as operations wait for confirmation
- Zero or near-zero data loss (RPO approaching zero)
- Example: Oracle Data Guard in maximum protection mode

**Asynchronous Replication**:
- Primary confirms write operations before replication completes
- Lower latency for write operations
- Potential for data loss during failures (higher RPO)
- Better performance over long distances
- Example: MySQL standard replication

### Cold, Warm, and Hot Standby Systems

**Cold Standby**:
- Backup systems are available but not configured and ready
- Longest recovery time (hours to days)
- Lowest cost option
- Example: Spare hardware stored in a warehouse

**Warm Standby**:
- Backup systems are configured and partially running
- Medium recovery time (minutes to hours)
- Data is periodically synchronized
- Example: Database server with scheduled data replication

**Hot Standby**:
- Backup systems are fully operational and ready to take over
- Shortest recovery time (seconds to minutes)
- Data is continuously synchronized
- Example: Active-Passive cluster with real-time replication

### RAID Levels for Storage Redundancy

RAID (Redundant Array of Independent Disks) provides different levels of storage redundancy:

| RAID Level | Description | Minimum Disks | Redundancy | Use Case |
|------------|-------------|---------------|------------|----------|
| RAID 0 | Striping (no redundancy) | 2 | None | Performance only |
| RAID 1 | Mirroring | 2 | Can lose 1 disk | System disks |
| RAID 5 | Striping with parity | 3 | Can lose 1 disk | General purpose |
| RAID 6 | Striping with dual parity | 4 | Can lose 2 disks | Large arrays |
| RAID 10 | Mirror + Stripe | 4 | Can lose multiple disks (with limitations) | Critical databases |

### Clustering

Clustering refers to connecting multiple computers to work as a single system. Different clustering approaches support high availability:

**Active-Active Clustering**:
- All nodes actively process requests
- Workload distributed across all nodes
- Requires state sharing or stateless design
- Better resource utilization

**Active-Passive Clustering**:
- Only one node actively processes requests
- Standby nodes monitor and take over if needed
- Simpler to implement for stateful applications
- Less efficient resource utilization

**Quorum Clustering**:
- Requires majority agreement for critical decisions
- Prevents split-brain scenarios
- Uses odd number of nodes (3, 5, 7)
- Common in distributed databases and consensus systems

## Common Technical Terms in Load Balancing

### L4 vs. L7 Load Balancing

These terms refer to the OSI model layers at which load balancing operates:

**L4 (Transport Layer) Load Balancing**:
- Works with TCP/UDP protocols
- Makes routing decisions based on IP addresses and ports
- Cannot inspect HTTP headers or content
- Faster and less resource-intensive
- Used for basic traffic distribution

**L7 (Application Layer) Load Balancing**:
- Works with application protocols (HTTP, HTTPS, etc.)
- Can route based on URLs, cookies, headers
- Provides content-based routing capabilities
- More CPU-intensive but more feature-rich
- Used for advanced application delivery

### Persistence Methods

Also called "sticky sessions" or "session affinity," persistence ensures a client's requests are directed to the same server:

**Cookie-Based Persistence**:
- Load balancer inserts a cookie identifying the server
- Subsequent requests include this cookie
- Most flexible method for web applications

**IP-Based Persistence**:
- Client's IP address determines server selection
- Uses a hash of the IP address
- Less accurate with NAT or proxy environments

**SSL Session ID Persistence**:
- Uses the SSL session ID to identify sessions
- Useful for HTTPS traffic
- Maintains persistence without application changes

### Connection Draining

Connection draining (or "graceful removal") is the process of removing a server from rotation without disrupting active connections:

1. Server is marked for removal
2. Load balancer stops sending new connections
3. Existing connections continue until completion
4. Server is removed only after all connections finish

This prevents service disruption during maintenance or scaling operations.

### Backend Server States

Load balancers track multiple states for backend servers:

**Healthy**:
- Server passes all health checks
- Receives normal traffic distribution

**Unhealthy**:
- Server fails health checks
- Removed from traffic rotation

**Draining**:
- Server completes existing sessions but receives no new traffic
- Used during maintenance or decommissioning

**Quiescing**:
- Similar to draining but with time limit
- After time limit, connections may be forcibly closed

### WAF (Web Application Firewall)

A WAF protects web applications by filtering and monitoring HTTP traffic:

- **Function**: Filters malicious web traffic (SQL injection, XSS, etc.)
- **Deployment**: Often integrated with load balancers
- **Rule Types**: Signature-based, behavioral, reputation-based
- **Examples**: ModSecurity, AWS WAF, Cloudflare WAF

## Additional Web Infrastructure Components

### CDN (Content Delivery Network)

A CDN distributes content geographically to reduce latency and improve availability:

- **Function**: Caches and delivers content from servers close to users
- **Benefits**: Reduces load on origin servers, improves page load times
- **Edge Locations**: Distributed servers in multiple geographic regions
- **Examples**: Cloudflare, Akamai, AWS CloudFront, Fastly

### API Gateway

An API gateway serves as the entry point for API calls:

- **Function**: Routes API requests, manages authentication, rate limiting
- **Benefits**: Centralizes API traffic management and security
- **Features**: Request transformation, response caching, analytics
- **Examples**: Kong, AWS API Gateway, Azure API Management

### Circuit Breaker

The circuit breaker pattern prevents cascading failures by temporarily disabling operations likely to fail:

- **States**: Closed (normal), Open (failing), Half-Open (testing recovery)
- **Function**: Detects failures and prevents operations during failure periods
- **Benefits**: Improves system resilience, prevents resource exhaustion
- **Examples**: Netflix Hystrix, resilience4j

## Glossary of Acronyms and Technical Terms

| Term | Definition |
|------|------------|
| ADC | Application Delivery Controller - advanced load balancer with additional features |
| Anycast | Routing method where traffic goes to the nearest instance of many using the same IP address |
| ARP | Address Resolution Protocol - maps IP addresses to MAC addresses |
| BGP | Border Gateway Protocol - core routing protocol of the internet |
| BMC | Baseboard Management Controller - server hardware management interface |
| CNAME | Canonical Name - DNS record type for domain aliases |
| DMZ | Demilitarized Zone - network segment between trusted and untrusted networks |
| HA | High Availability - system design for continuous operation |
| IPMI | Intelligent Platform Management Interface - standard for server hardware monitoring |
| LUN | Logical Unit Number - identifier for a logical storage device |
| MAC Address | Media Access Control Address - hardware identifier for network interfaces |
| NAT | Network Address Translation - maps private IPs to public IPs |
| PDU | Power Distribution Unit - device that distributes electric power to servers |
| QPS | Queries Per Second - performance metric for servers |
| RPO | Recovery Point Objective - maximum acceptable data loss |
| RTO | Recovery Time Objective - maximum acceptable downtime |
| SAN | Storage Area Network - dedicated network for storage access |
| SDN | Software-Defined Networking - programmable network management |
| SPOF | Single Point of Failure - component that causes system failure if it fails |
| SSL/TLS | Secure Sockets Layer/Transport Layer Security - encryption protocols |
| STONITH | Shoot The Other Node In The Head - isolation mechanism for failed servers |
| TTL | Time To Live - lifespan setting for cached data |
| UPS | Uninterruptible Power Supply - battery backup power system |
| VIP | Virtual IP - IP address shared between redundant devices |
| VLAN | Virtual LAN - logical subdivision of a network |
| VPN | Virtual Private Network - secure connection over public network |
| VRRP | Virtual Router Redundancy Protocol - automatic router failover protocol |
| WAF | Web Application Firewall - security device for web applications |

## Comprehensive Web Infrastructure Lexicon

This lexicon provides concise definitions for technical terms used throughout web infrastructure design, operation, and architecture.

### A

**Access Control List (ACL)**: A list of permissions attached to an object that specifies which users or system processes are granted access and what operations are allowed.

**Active Directory**: Microsoft's directory service for Windows domain networks, providing authentication, directory, policy, and other services.

**Affinity**: In load balancing, directing a client's requests to the same server that handled their previous requests; also called session stickiness.

**Agent**: Software that runs on a system to collect data or perform actions on behalf of another application.

**Agile Infrastructure**: IT infrastructure that can rapidly adapt to changing business requirements through automation and flexible design.

**AnyCast**: A network addressing method where data is routed to the "nearest" or "best" destination as viewed by the routing topology.

**API (Application Programming Interface)**: A set of rules and protocols that allows one software application to interact with another.

**Artifact**: A file or package produced during the software development process, such as compiled code or container images.

**Auto-scaling**: The automatic adjustment of computational resources based on workload.

### B

**Backend**: The server-side of an application that handles data processing and business logic.

**Backpressure**: A mechanism to provide resistance to data flow when a component becomes overloaded, preventing system failure.

**Backup**: A copy of data that can be restored if the original is lost or damaged.

**Bandwidth**: The maximum data transfer rate of a network or internet connection.

**Bastion Host**: A specially hardened server designed to withstand attacks, placed at the edge of a network to provide secure access to resources.

**Blue-Green Deployment**: A release technique using two identical production environments, with only one active at a time, allowing for seamless cutover.

**Bootstrapping**: The process of loading the initial set of instructions when powering on a computer or server.

**Bridge**: A network device that connects two network segments at the data link layer (Layer 2).

**Brownfield Deployment**: Deploying new systems within an existing legacy environment, as opposed to starting from scratch.

**Bulkhead Pattern**: An architecture pattern that isolates elements of an application into pools so that if one fails, the others will continue to function.

### C

**Cache**: A temporary storage area for frequently accessed data to reduce access time.

**Capacity Planning**: The process of determining the resources needed to meet future demands on an IT system.

**Canary Deployment**: A release strategy where new code is deployed to a small subset of servers or users before a full rollout.

**CDN (Content Delivery Network)**: A geographically distributed network of servers that delivers web content to users based on their geographic location.

**Certificate**: A digital document that validates the identity of a website or service.

**Change Management**: The process for controlling changes to IT systems with minimal disruption.

**CI/CD (Continuous Integration/Continuous Deployment)**: Development practices that enable frequent, automated application updates.

**Circuit Breaker**: A design pattern that prevents cascading failures by detecting failures and preventing operation when the failure rate exceeds a threshold.

**Cluster**: A group of servers that work together as a single system to provide high availability and load balancing.

**Cold Start**: The process of initializing a system from a completely powered-off state, often taking longer than a warm start.

**Configuration Drift**: The phenomenon where server configurations deviate from their documented or desired state due to manual changes.

**Connection Pooling**: A cache of database connections maintained so that connections can be reused when future requests to the database are required.

**Connection Draining**: The process of allowing existing connections to complete before removing a server from a load-balanced pool.

**Control Plane**: The part of a network that carries signaling traffic, determining how data should be forwarded.

**Cookie**: Small pieces of data stored on a user's computer by the web browser while browsing a website.

**CORS (Cross-Origin Resource Sharing)**: A security feature that allows or restricts web applications from making requests to a domain different from the one that served the original application.

**Credential Rotation**: The practice of regularly changing authentication credentials to minimize the risk of unauthorized access.

### D

**Data Center**: A facility used to house computer systems and associated components.

**Data Plane**: The part of a network that carries user traffic.

**Dead Letter Queue**: A storage mechanism for messages that cannot be delivered to their intended recipients.

**Dependency Hell**: Complications arising from version conflicts between software dependencies.

**Deployment Pipeline**: An automated manifestation of the process for getting software from version control to users.

**DevOps**: A set of practices that combines software development and IT operations to shorten development cycles and deliver features continuously.

**Disaster Recovery**: A set of policies and procedures to enable the recovery of vital technology infrastructure after a natural or human-induced disaster.

**DNS Poisoning**: A cyberattack where corrupt DNS data is introduced into the DNS resolver's cache, causing traffic to be diverted to malicious websites.

**Docker**: A platform that packages applications into containers for development, shipping, and running applications.

**Downtime**: The period during which a system is unavailable or offline.

### E

**Edge Computing**: Processing data near the source of data generation rather than in a centralized data center.

**Elasticity**: The ability of a system to adapt to workload changes by automatically provisioning and de-provisioning resources.

**Endpoint**: Any device that connects to a network, such as a laptop, desktop, server, or IoT device.

**Ephemeral**: Temporary or short-lived, often referring to instances or containers designed to be regularly replaced rather than updated in place.

**ETL (Extract, Transform, Load)**: A process in database usage that extracts data from sources, transforms it to fit operational needs, and loads it into the end target database.

**Event-Driven Architecture**: A software architecture pattern promoting the production, detection, consumption of, and reaction to events.

**Eventual Consistency**: A consistency model where, given enough time, all updates to a distributed system will propagate through the system.

### F

**Failback**: The process of restoring operations to the primary system after a failover to a backup system.

**Failover**: Automatically switching to a redundant or standby system upon failure of the primary system.

**Fault Domain**: A set of hardware components (computers, switches, etc.) that share a common point of failure.

**Fault Tolerance**: The property that enables a system to continue operating properly in the event of the failure of some of its components.

**Federation**: A form of system integration where certain resources are shared while others remain under local control.

**Frontend**: The part of a website or application that users interact with directly.

**Full-Stack**: Referring to the entire range of technologies involved in web development, from front-end to back-end.

### G

**Gateway**: A node in a computer network that connects two different networks.

**Geofencing**: Using GPS or RFID technology to create a virtual geographic boundary, enabling software to trigger a response when a mobile device enters or leaves a particular area.

**Graceful Degradation**: The ability of a system to maintain limited functionality even when a large portion of it is inoperative.

**Greenfield Deployment**: A project that lacks constraints imposed by prior work, typically starting from scratch with no legacy systems.

**GUI (Graphical User Interface)**: A form of user interface that allows users to interact with electronic devices through graphical icons and visual indicators.

### H

**HAProxy**: A free, open-source load balancer and proxy server for TCP and HTTP-based applications.

**Hard Restart**: Restarting a system by cutting power completely then reapplying it, rather than using operating system commands.

**Hash Function**: An algorithm that maps data of arbitrary size to fixed-size values, used in load balancing, data verification, and cryptography.

**Health Check**: A method to verify that a system is functioning correctly, often performed by load balancers to determine where to route traffic.

**Heartbeat**: A periodic signal generated by hardware or software to indicate it is still active and functioning.

**Horizontal Scaling**: Adding more machines or instances to a system to handle increased load, as opposed to vertical scaling.

**Hot Swappable**: Components that can be replaced while the system is running without causing downtime.

**Hybrid Cloud**: A computing environment that uses a mix of on-premises, private cloud, and public cloud services.

**Hypervisor**: Software, firmware, or hardware that creates and runs virtual machines.

### I

**Idempotent**: An operation that produces the same result no matter how many times it is performed.

**IaC (Infrastructure as Code)**: Managing and provisioning computing infrastructure through machine-readable definition files rather than physical hardware configuration.

**Immutable Infrastructure**: An approach where servers are never modified after deployment; if changes are needed, new servers are built and deployed.

**Ingress**: A network entry point or the act of data entering a network.

**Instance**: A single occurrence of a running application, server, or virtual machine.

**Integration Testing**: Testing in which individual software modules are combined and tested as a group.

**IP Address Pool**: A range of IP addresses that can be assigned to devices on a network.

**IPsec (Internet Protocol Security)**: A protocol suite for securing Internet Protocol communications by authenticating and encrypting each IP packet of a communication session.

### J

**Jenkins**: An open-source automation server that helps automate parts of software development related to building, testing, and deploying.

**Jitter**: Variability in packet delay, causing inconsistent data transfer rates.

**JSON (JavaScript Object Notation)**: A lightweight data-interchange format that is easy for humans to read and write and easy for machines to parse and generate.

### K

**Kafka**: A distributed streaming platform capable of handling trillions of events a day.

**Kubernetes**: An open-source platform for automating deployment, scaling, and management of containerized applications.

### L

**Latency**: The time delay between the cause and effect of a physical change in a system.

**LDAP (Lightweight Directory Access Protocol)**: An open, vendor-neutral protocol for accessing and maintaining distributed directory information services.

**Least Privilege**: The principle that a user or system should have only the minimum levels of access necessary to complete their job functions.

**Legacy System**: An old computer system, programming language, software application, or process that continues to be used despite its obsolescence.

**Load Testing**: Testing that determines a system's behavior under both normal and anticipated peak load conditions.

**Locking**: A mechanism that restricts access to a resource in an environment where there are many threads of execution.

**Logging**: The practice of recording events, operations, messages, and other data from applications and systems.

**Long-Tail Latency**: The phenomenon where a small percentage of requests take significantly longer to process than the majority.

### M

**Managed Service**: A service that is operated and maintained by a third-party provider.

**Mesh Network**: A network topology where each node can relay data for the network, often used for IoT or resilient communications.

**Metadata**: Data that provides information about other data, often used to facilitate the understanding, use, and management of data.

**Microservice**: A software development technique where an application is structured as a collection of loosely coupled services.

**Middleware**: Software that acts as a bridge between an operating system and applications, especially on a network.

**Mitigation**: Actions taken to reduce the severity or impact of an incident or vulnerability.

**Monolithic Architecture**: A software design where all functionality is contained within a single program or codebase.

**Multi-Tenancy**: A software architecture in which a single instance of software serves multiple customers (tenants).

**Multi-factor Authentication (MFA)**: An authentication method requiring users to provide two or more verification factors to gain access.

### N

**Network Interface Card (NIC)**: Hardware component connecting a computer to a network.

**Network Segmentation**: Dividing a network into multiple segments to improve performance and security.

**Node**: Any single device connected to a network, or a processing location in a distributed system.

**NoSQL**: A type of database that provides a mechanism for storage and retrieval of data that is modeled differently from traditional relational databases.

**N-Tier Architecture**: A client-server architecture with multiple layers (tiers) that separates different application functions.

### O

**OAuth**: An open standard for access delegation, commonly used for secure API authorization.

**Observability**: The ability to measure a system's current state based on the data it generates, such as logs, metrics, and traces.

**On-Premises**: Software and technology that is located within the physical confines of an organization, often in their data center.

**Orchestration**: The automated configuration, coordination, and management of computer systems, applications, and services.

**Origin Server**: The server that hosts the original version of web content, as opposed to cached copies on CDNs.

**Outage**: A period when a system or service is unavailable or fails to provide expected service.

### P

**PaaS (Platform as a Service)**: A category of cloud computing services that provides a platform allowing customers to develop, run, and manage applications.

**Packet**: A unit of data transmitted over a network.

**Partition Tolerance**: The ability of a distributed system to continue operating despite arbitrary message loss or failure of part of the system.

**Patch Management**: The process of distributing and applying updates to software.

**Payload**: The actual data transmitted in a network packet, API request, or other communication method.

**Peer-to-Peer (P2P)**: A distributed architecture where tasks are shared among peers without a central server.

**Performance Testing**: Testing to determine system responsiveness and stability under a particular workload.

**Persistence**: The characteristic of data that outlives the execution of the program that created it.

**Phoenix Server**: A server that is regularly destroyed and rebuilt from configuration files, rather than maintained over time.

**Platform Engineering**: The discipline of building and operating self-service internal developer platforms.

**Pod**: The smallest deployable unit in Kubernetes that can contain one or more containers.

**Polling**: The process where a client repeatedly checks a server for new information or status updates.

**Proxy Server**: A server that acts as an intermediary for requests from clients seeking resources from other servers.

### Q

**QoS (Quality of Service)**: The description or measurement of the overall performance of a service, particularly in terms of users' experience.

**Queue**: A data structure that follows the First-In-First-Out (FIFO) principle, often used in message-passing systems.

**Quorum**: The minimum number of members of a group that must be present to make the proceedings of that meeting valid.

### R

**RBAC (Role-Based Access Control)**: A method of restricting network access based on the roles of individual users within an organization.

**Rate Limiting**: The process of controlling the amount of requests a user can make to an API or service within a given timeframe.

**Real-Time Processing**: The processing of data immediately as it is received, with minimal delay.

**Recovery Point Objective (RPO)**: The maximum targeted period in which data might be lost due to a major incident.

**Recovery Time Objective (RTO)**: The targeted duration of time within which a business process must be restored after a disaster.

**Registry**: A centralized repository for storing and retrieving docker images or other artifacts.

**Regression Testing**: Testing to confirm that recent program changes have not adversely affected existing features.

**Replica**: A copy of data or a service that provides redundancy and improves availability.

**Resilience**: The ability of a system to withstand and recover from failures and disruptions.

**REST (Representational State Transfer)**: An architectural style for designing networked applications, typically using HTTP methods.

**Retry Pattern**: A resilience pattern where an operation is attempted multiple times if the initial attempt fails.

**Rollback**: The process of reverting to a previous version of software after a deployment failure.

**Router**: A networking device that forwards data packets between computer networks.

**RPC (Remote Procedure Call)**: A protocol that allows a program on one computer to cause a subroutine on another computer to execute without explicitly coding the details of the remote interaction.

### S

**SaaS (Software as a Service)**: A software distribution model where applications are hosted by a service provider and made available to users over the internet.

**Scalability**: The capability of a system to handle a growing amount of work, or its potential to be enlarged to accommodate that growth.

**Schema**: The structure or format of data in a database, defining how data is organized and how relationships between data are governed.

**SDN (Software-Defined Networking)**: An approach to network management that enables dynamic, programmatically efficient network configuration.

**Secret Management**: The secure storage, access, and management of sensitive information such as passwords, API keys, and certificates.

**Security Group**: A virtual firewall that controls inbound and outbound traffic to cloud resources.

**Serialization**: The process of converting an object into a format that can be stored or transmitted and reconstructed later.

**Server Farm**: A collection of computer servers usually maintained by an organization to supply server functionality.

**Service Discovery**: The automatic detection of devices and services on a network.

**Service Level Agreement (SLA)**: A commitment between a service provider and a client about various aspects of the service including availability and performance.

**Service Mesh**: A dedicated infrastructure layer for facilitating service-to-service communications between services in a microservices architecture.

**Session**: A temporary and interactive information interchange between two or more communicating devices.

**Sharding**: A database architecture pattern related to horizontal partitioning, where data is split across multiple databases according to a shard key.

**Shared Nothing Architecture**: A distributed computing architecture where each node is independent and self-sufficient.

**Shutdown**: The process of stopping a system gracefully, ensuring all data is saved and processes terminate properly.

**Sidecar Pattern**: A design pattern where a separate container or process is deployed alongside the main application to provide supporting features.

**Single Sign-On (SSO)**: An authentication scheme that allows a user to log in with a single ID and password to access multiple related, but independent, systems.

**SOAP (Simple Object Access Protocol)**: A messaging protocol specification for exchanging structured information in web services.

**Socket**: A software structure that serves as an endpoint for sending and receiving data across a network.

**Soft Restart**: Restarting a system using operating system commands rather than cycling the power.

**Stateful**: Referring to applications or systems that save client data from one session to the next.

**Stateless**: Referring to applications or protocols that do not store information about client sessions.

**Stress Testing**: A form of testing that evaluates how a system performs under extreme conditions, often beyond normal operational capacity.

**Subnet**: A logical subdivision of an IP network, allowing for more efficient use of IP addresses.

**Synchronous**: Operations that block or wait for a response before continuing.

### T

**Tenant**: In multi-tenancy architectures, a single instance of software serves multiple customers (tenants).

**Throttling**: Controlling the rate at which an application or user can use resources or perform operations.

**Throughput**: The amount of data or operations that can be processed in a given amount of time.

**Time-Series Database**: A database optimized for time-stamped or time-series data, commonly used for monitoring and metrics.

**TLS (Transport Layer Security)**: Cryptographic protocols designed to provide communications security over a computer network.

**Token**: A piece of data that represents the right to perform an operation or access a resource.

**Tracing**: Following the path of a request through various services and components in a distributed system.

**Traffic Shaping**: The control of network traffic to optimize performance by delaying some packets while prioritizing others.

**Transaction**: A sequence of operations performed as a single logical unit of work, often in a database.

### U

**Uptime**: The amount of time a service or system is operational and available.

**URI (Uniform Resource Identifier)**: A string of characters that unambiguously identifies a resource.

**User Agent**: A software agent that acts on behalf of a user, such as a web browser.

**UUID (Universally Unique Identifier)**: A 128-bit number used to identify information in computer systems.

### V

**Validation**: The process of ensuring data meets specified criteria before being processed.

**vCPU (Virtual CPU)**: A share of a physical CPU that is assigned to a virtual machine.

**Vertical Scaling**: Increasing the capacity of a single server by adding more resources (CPU, RAM), as opposed to horizontal scaling.

**Virtual Machine (VM)**: A software emulation of a computer system that provides the functionality of a physical computer.

**Virtual Network**: A computer network that consists of virtual network links, logical rather than physical connections between nodes.

**VLAN (Virtual Local Area Network)**: A logical grouping of devices on one or more LANs configured to communicate as if they were on the same LAN.

**VPC (Virtual Private Cloud)**: A private cloud computing environment within a public cloud.

**VPN (Virtual Private Network)**: Extends a private network across a public network, enabling users to send and receive data as if their devices were directly connected to the private network.

### W

**WAF (Web Application Firewall)**: A firewall that filters, monitors, and blocks HTTP traffic to and from a web service.

**Warm Standby**: A secondary system that is partially operational and can be quickly brought to full operational status.

**Warm-Up**: The process of gradually increasing traffic to a new or recovered server to allow it to build its caches and establish connections before handling full load.

**Web Hook**: User-defined HTTP callbacks triggered by specific events.

**Wildcard Certificate**: An SSL certificate that can secure a domain and multiple subdomains.

**Worker**: A process that performs tasks in the background, separate from the main application thread or process.

### X

**X.509**: A standard defining the format of public key certificates, used in TLS/SSL protocols.

**XML (eXtensible Markup Language)**: A markup language that defines a set of rules for encoding documents in a format that is both human-readable and machine-readable.

### Y

**YAML (YAML Ain't Markup Language)**: A human-readable data serialization standard often used for configuration files.

### Z

**Zero Downtime Deployment**: A deployment approach that ensures service availability throughout the update process.

**Zero Trust**: A security concept based on the belief that organizations should not automatically trust anything inside or outside its perimeters and must verify everything trying to connect to its systems.