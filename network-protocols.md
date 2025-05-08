# Network Protocols and Ports Reference

## Introduction
Network protocols are standardized rules that allow different devices to communicate on a network. Ports are numerical identifiers (0-65535) that specify which application or service should receive network traffic on a device. Together, they form the foundation of all network communications.

## Common Network Protocols by Category

### Web Protocols

#### HTTP (Hypertext Transfer Protocol)
- **Port:** 80
- **Role:** Transfers hypertext (web pages) between servers and clients
- **History:** Developed in 1989-1991 by Tim Berners-Lee at CERN
- **Evolution:** HTTP/0.9 (1991) → HTTP/1.0 (1996) → HTTP/1.1 (1997) → HTTP/2 (2015) → HTTP/3 (2022)
- **Notes:** Unencrypted; largely replaced by HTTPS for security reasons

#### HTTPS (HTTP Secure)
- **Port:** 443
- **Role:** Encrypted version of HTTP using TLS/SSL
- **History:** Initially developed by Netscape in 1994
- **Evolution:** HTTPS with SSL → HTTPS with TLS 1.0, 1.1, 1.2, 1.3 (current standard)

### Email Protocols

#### SMTP (Simple Mail Transfer Protocol)
- **Port:** 25 (standard), 465 (SSL), 587 (TLS/submission)
- **Role:** Handles outgoing email delivery
- **History:** Defined in 1982 (RFC 821)
- **Evolution:** Extended SMTP (ESMTP) added in 1995 (RFC 1869)

#### POP3 (Post Office Protocol v3)
- **Port:** 110 (standard), 995 (SSL/TLS)
- **Role:** Retrieves email from server to client; typically downloads and removes messages from server
- **History:** POP1 (1984) → POP2 (1985) → POP3 (1988)
- **Evolution:** Being replaced by IMAP in many contexts

#### IMAP (Internet Message Access Protocol)
- **Port:** 143 (standard), 993 (SSL/TLS)
- **Role:** Retrieves email while keeping messages on the server; allows multiple clients to manage same mailbox
- **History:** IMAP2 (1988) → IMAP3 (1990) → IMAP4 (1994) → IMAP4rev1 (current)

### File Transfer Protocols

#### FTP (File Transfer Protocol)
- **Port:** 20 (data), 21 (control)
- **Role:** Transfers files between client and server
- **History:** Developed in 1971, standardized in 1985
- **Evolution:** Being replaced by more secure protocols like SFTP and FTPS

#### SFTP (SSH File Transfer Protocol)
- **Port:** 22 (same as SSH)
- **Role:** Secure file transfer using SSH encryption
- **History:** Developed in the late 1990s
- **Notes:** Not related to FTP despite similar name; completely different protocol

#### FTPS (FTP Secure)
- **Port:** 990 (implicit TLS), 21 (explicit TLS/control), 989 (implicit TLS/data)
- **Role:** FTP with added TLS/SSL encryption
- **History:** Developed in the late 1990s

### Remote Access Protocols

#### Telnet
- **Port:** 23
- **Role:** Provides terminal emulation for remote access
- **History:** Developed in 1969
- **Evolution:** Largely replaced by SSH due to lack of encryption

#### SSH (Secure Shell)
- **Port:** 22
- **Role:** Encrypted remote login and command execution
- **History:** SSH-1 (1995) → SSH-2 (2006, current standard)
- **Notes:** Replaced Telnet and rlogin as the secure alternative

#### RDP (Remote Desktop Protocol)
- **Port:** 3389
- **Role:** Provides graphical interface to connect to another computer
- **History:** Introduced by Microsoft in 1998
- **Evolution:** Enhanced with encryption, multi-monitor support, etc.

### Name Resolution and Network Configuration

#### DNS (Domain Name System)
- **Port:** 53 (TCP and UDP)
- **Role:** Translates domain names to IP addresses
- **History:** Introduced in 1983, replacing host files
- **Evolution:** DNS over TLS (DoT, port 853) and DNS over HTTPS (DoH, port 443) added for privacy

#### DHCP (Dynamic Host Configuration Protocol)
- **Port:** 67 (server), 68 (client)
- **Role:** Automatically assigns IP addresses and network configuration to devices
- **History:** Developed in 1993, evolved from BOOTP
- **Evolution:** DHCPv6 developed for IPv6 networks

### Network Management

#### SNMP (Simple Network Management Protocol)
- **Port:** 161 (queries), 162 (traps)
- **Role:** Monitors and manages network devices
- **History:** SNMPv1 (1988) → SNMPv2 (1993) → SNMPv3 (1999)
- **Evolution:** SNMPv3 added security features lacking in earlier versions

### File Sharing

#### SMB/CIFS (Server Message Block/Common Internet File System)
- **Port:** 445 (direct TCP/IP); formerly 139 (over NetBIOS)
- **Role:** Provides shared access to files, printers, and other resources
- **History:** Created by IBM in the 1980s, adopted and modified by Microsoft
- **Evolution:** SMB1 → SMB2 (2006) → SMB3 (2012) with encryption and performance improvements

#### NFS (Network File System)
- **Port:** 2049
- **Role:** Distributed file system protocol allowing access to files over a network
- **History:** Developed by Sun Microsystems in 1984
- **Evolution:** NFSv2 (1989) → NFSv3 (1995) → NFSv4 (2000) → NFSv4.1/4.2 (current)

### Real-time Communication

#### SIP (Session Initiation Protocol)
- **Port:** 5060 (standard), 5061 (TLS)
- **Role:** Signals and controls multimedia communication sessions
- **History:** Developed in 1996, standardized in 1999
- **Notes:** Common in VoIP and video conferencing

#### RTP (Real-time Transport Protocol)
- **Port:** Dynamic ports, typically 16384-32767
- **Role:** Delivers audio and video over IP networks
- **History:** First published in 1996
- **Evolution:** SRTP (Secure RTP) added encryption

### Database Protocols

#### MySQL
- **Port:** 3306
- **Role:** Client-server communication for MySQL databases

#### PostgreSQL
- **Port:** 5432
- **Role:** Client-server communication for PostgreSQL databases

#### MSSQL (Microsoft SQL Server)
- **Port:** 1433
- **Role:** Client-server communication for Microsoft SQL Server

## Historical Context

### The Evolution of Network Security
Early protocols like Telnet, FTP, and HTTP transmitted data in plaintext, making them vulnerable to eavesdropping. Modern networks prioritize secure alternatives (SSH instead of Telnet, SFTP instead of FTP, and HTTPS instead of HTTP).

### The Transition from IPv4 to IPv6
- **IPv4:** Uses 32-bit addresses (4.3 billion possible addresses)
- **IPv6:** Uses 128-bit addresses (340 undecillion addresses)
- **Reason for transition:** IPv4 address exhaustion due to the explosive growth of internet-connected devices

### Port Classification
- **Well-known ports:** 0-1023 (require admin/root privileges)
- **Registered ports:** 1024-49151 (assigned by IANA)
- **Dynamic/Private ports:** 49152-65535 (used for temporary connections)

## Best Practices
1. **Security:** Close unused ports to reduce attack surface
2. **Configuration:** Use standard ports unless there's a good reason not to
3. **Documentation:** Maintain documentation of non-standard port usage
4. **Testing:** Regularly test network connectivity and security
5. **Updates:** Keep network software updated to address vulnerabilities

## Conclusion
Understanding network protocols and ports is fundamental to network administration, security, and troubleshooting. As the internet has evolved, protocols have been updated or replaced to address security concerns and new requirements.
