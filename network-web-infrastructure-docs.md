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
