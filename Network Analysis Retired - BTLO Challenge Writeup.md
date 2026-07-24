# Blue Team Labs Online - Network Analysis Writeup

## Challenge Information

| Category | Details |
|----------|----------|
| Platform | Blue Team Labs Online (BTLO) |
| Challenge | Network Analysis |
| Difficulty | Easy |
| Category | Network Forensics / Packet Analysis / Defensive Security |

---

# Introduction

Today we are back with another **Blue Team Labs Online (BTLO)** defensive security challenge named **Network Analysis**.

Unlike traditional penetration testing challenges, this room places us in the role of a **Security Operations Center (SOC) Analyst** responsible for investigating suspicious network activity captured inside a packet capture (PCAP) file.

Instead of exploiting vulnerabilities, our objective is to analyze captured network traffic to determine whether suspicious behavior is malicious, identify attacker activity, reconstruct the attack timeline, and answer questions based entirely on network evidence.

This challenge introduces fundamental packet analysis techniques that every SOC analyst, Incident Responder, and Digital Forensics investigator should understand.

---

# Scenario

The challenge provides the following scenario:

> The SOC received an alert from its SIEM regarding **Local-to-Local Port Scanning**, where one internal host appeared to be scanning another internal system. Your task is to investigate the supplied packet capture (PCAP) and determine whether this activity is malicious by analyzing the captured network traffic.

Throughout the investigation we will use **Wireshark** to inspect packets, identify reconnaissance activity, detect web exploitation, and follow the attack until the attacker successfully gains remote shell access.

---

# Objectives

The objectives of this challenge include:

- Analyze a PCAP file.
- Identify the scanning host.
- Determine the targeted ports.
- Detect the scanning technique.
- Identify reconnaissance tools.
- Investigate HTTP requests.
- Detect web shell uploads.
- Follow attacker command execution.
- Understand reverse shell activity.
- Reconstruct the complete attack timeline.

---

# About PCAP Analysis

Packet Capture (PCAP) files contain raw network traffic collected from an interface.

Unlike application logs, PCAPs preserve every packet transmitted across the network, allowing investigators to reconstruct attacker behavior from beginning to end.

Using Wireshark, analysts can examine:

- TCP sessions
- HTTP requests
- DNS lookups
- SMB traffic
- FTP transfers
- Command execution
- File uploads
- Reverse shells

For this reason, packet analysis is one of the most valuable skills in Digital Forensics and Incident Response (DFIR).

---

# Opening the Capture

The challenge provides a compressed archive containing a packet capture.

After downloading the archive, it is extracted to reveal the `.pcap` file.

The capture is then opened in **Wireshark** for investigation.

Rather than scrolling through thousands of packets manually, Wireshark provides several powerful analysis features including:

- Conversations
- Endpoints
- Protocol Hierarchy
- Statistics
- Display Filters
- Follow TCP Stream

These features significantly reduce investigation time.

---

# Question 1

## What IP address is responsible for conducting the port scan activity?

### Answer

```
10.251.96.4
```

---

# Identifying the Scanning Host

A good starting point during any packet investigation is identifying which systems are communicating.

Within Wireshark navigate to:

```
Statistics
        ↓
Conversations
        ↓
TCP
```

The **TCP Conversations** window displays all TCP communications occurring within the packet capture.

Sorting by destination ports immediately reveals one host communicating with an unusually large number of ports.

The source IP performing this activity is:

```
10.251.96.4
```

Since the host attempts connections against numerous ports on another internal system, this behavior strongly indicates reconnaissance through port scanning.

---

# Understanding Port Scanning

Port scanning is one of the earliest phases of most cyber attacks.

Attackers perform scans to discover:

- Running services
- Open ports
- Firewalls
- Operating systems
- Potential attack vectors

By identifying exposed services before exploitation, attackers can determine which vulnerabilities may exist.

Consequently, unexpected internal port scanning often generates alerts inside enterprise SIEM platforms.

---

# Question 2

## What is the port range scanned?

### Answer

```
1-1024
```

---

# Understanding the Port Range

Examining the destination ports within the TCP conversations shows the attacker scanning ports throughout the **well-known port range**.

```
1 — 1024
```

These ports host the most common network services.

Examples include:

| Port | Service |
|-------|----------|
|21|FTP|
|22|SSH|
|23|Telnet|
|25|SMTP|
|53|DNS|
|80|HTTP|
|110|POP3|
|139|NetBIOS|
|143|IMAP|
|443|HTTPS|

Attackers commonly begin reconnaissance with these ports because they frequently expose externally accessible services.

---

# Question 3

## What type of port scan was conducted?

### Answer

```
SYN Scan
```

---

# Identifying the Scan Type

Determining the scan type requires examining the TCP packets exchanged during reconnaissance.

Instead of completing full TCP connections, the scanning host sends only **TCP SYN** packets toward numerous destination ports.

The responses determine whether each port is:

- Open
- Closed
- Filtered

Because the connection is never fully established, this technique is commonly known as a **TCP SYN Scan** or **Half-Open Scan**.

This scan is popular because it is:

- Fast
- Efficient
- Less likely to appear in traditional connection logs
- Supported by tools such as Nmap

---

# TCP Three-Way Handshake

A normal TCP connection follows three steps:

```
Client
   │
   │ SYN
   ▼
Server
   │
   │ SYN-ACK
   ▼
Client
   │
   │ ACK
   ▼
Connection Established
```

During a SYN scan, the attacker intentionally avoids completing the final ACK step, allowing ports to be identified without fully opening connections.

---

# Question 4

## Two additional tools were used during reconnaissance. What are they?

### Answer

```
Gobuster

SQLMap
```

---

# Discovering Reconnaissance Tools

Wireshark records HTTP request headers, including the **User-Agent** string.

Filtering HTTP traffic reveals requests containing recognizable user-agent values.

The first identifies:

```
Gobuster/3.0.1
```

Gobuster is commonly used for:

- Directory discovery
- Hidden files
- Virtual hosts
- Backup files

Later within the capture another user-agent appears.

```
sqlmap/1.4.7
```

SQLMap is an automated SQL Injection framework capable of:

- Database fingerprinting
- SQL Injection exploitation
- Database dumping
- Operating system command execution
- File upload

The appearance of both tools indicates the attacker is progressing from reconnaissance toward active exploitation.

---

# Understanding the Attack Progression

The observed sequence follows a common penetration testing methodology.

```
Port Scan
      │
      ▼
Directory Enumeration
      │
      ▼
SQL Injection Testing
      │
      ▼
Web Exploitation
```

Each stage builds upon the information gathered during the previous phase.

---

# Question 5

## What is the name of the PHP file through which the attacker uploaded the web shell?

### Answer

```
editprofile.php
```

---

# Investigating HTTP POST Requests

File uploads generally occur through **HTTP POST** requests.

Instead of inspecting every HTTP packet manually, Wireshark's display filters can isolate upload activity.

```
http.request.method == POST
```

Filtering POST requests immediately reduces the investigation to only requests capable of sending files or form data.

Reviewing these packets reveals several SQLMap requests followed by a POST request associated with file uploading.

The request references:

```
upload.php
```

Examining the HTTP headers more closely reveals the **Referer** field.

```
Referer:
http://10.251.96.4/editprofile.php
```

This indicates that the attacker uploaded the malicious payload through:

```
editprofile.php
```

Rather than directly accessing the upload endpoint, the attacker first interacted with the application's profile editing page, which ultimately submitted the uploaded file to the backend upload handler.

---

# Why HTTP POST Matters

HTTP supports multiple request methods.

The two most common are:

| Method | Purpose |
|----------|----------|
|GET|Retrieve information|
|POST|Submit information|

Because file uploads transmit data to the server, they almost always use **POST** requests.

Filtering POST traffic is therefore one of the quickest methods for locating malicious uploads inside packet captures.

---

# Part 1 Summary

During the first phase of the investigation, we identified the internal host **10.251.96.4** performing reconnaissance against another internal system. Analysis of the TCP conversations revealed a **SYN scan** targeting the well-known port range **1–1024**, indicating systematic service discovery. Further inspection of HTTP traffic uncovered the use of automated reconnaissance tools including **Gobuster** and **SQLMap**, demonstrating a transition from network enumeration to web application testing. Finally, by filtering **HTTP POST** requests, we identified **editprofile.php** as the page used to upload the attacker's web shell, marking the beginning of the exploitation phase. In the next section, we will follow the uploaded web shell, analyze command execution, investigate the reverse shell connection, and reconstruct the complete attack timeline.

# Question 6

## What is the name of the web shell uploaded by the attacker?

### Answer

```
dbfunctions.php
```

---

# Identifying the Web Shell

After discovering that the attacker successfully uploaded a malicious file through **editprofile.php**, the next objective is identifying the uploaded payload.

Continuing to inspect the HTTP requests following the upload reveals requests targeting a new PHP file.

```
dbfunctions.php
```

Unlike normal application files, this PHP script begins executing operating system commands supplied by the attacker.

This behavior clearly indicates that the uploaded file is acting as a **web shell**.

---

# Understanding Web Shells

A web shell is a malicious server-side script that allows attackers to execute commands directly on the compromised web server.

Common web shell capabilities include:

- Executing operating system commands
- Uploading files
- Downloading files
- Creating reverse shells
- Enumerating users
- Reading sensitive files
- Privilege escalation

Because web shells execute through the web server itself, they often inherit the permissions of the web service account.

---

# Question 7

## What parameter is used by the web shell to execute commands?

### Answer

```
cmd
```

---

# Investigating Command Execution

Inspecting the HTTP requests sent to the uploaded web shell reveals attacker-controlled parameters.

Within the request, the attacker supplies:

```
cmd
```

This parameter accepts arbitrary operating system commands.

An example request resembles:

```
dbfunctions.php?cmd=id
```

The web shell receives the value of the **cmd** parameter and executes it directly on the server.

This represents a classic example of **Operating System Command Execution**.

---

# Understanding Command Injection

Many simple web shells use a single parameter to pass commands.

Typical examples include:

```
cmd

exec

command

shell

run
```

Poorly secured applications may also unintentionally expose similar functionality through vulnerable scripts.

Monitoring HTTP parameters capable of executing system commands is an important detection opportunity for defenders.

---

# Question 8

## What is the first command executed by the attacker?

### Answer

```
id
```

---

# Understanding the First Command

Immediately after obtaining command execution, attackers typically verify their level of access.

The first observed command is:

```bash
id
```

This Linux command displays information about the currently authenticated user.

Typical output includes:

- User ID (UID)
- Group ID (GID)
- Supplementary groups

For example:

```text
uid=33(www-data)
gid=33(www-data)
groups=33(www-data)
```

Attackers commonly execute this command first because it immediately confirms:

- Whether code execution succeeded.
- Which user account is executing commands.
- The privileges currently available.

This information determines the next stage of the attack.

---

# Question 9

## What type of shell connection does the attacker obtain?

### Answer

```
Reverse Shell
```

---

# Understanding Reverse Shells

After verifying code execution, the attacker no longer relies solely on individual HTTP requests.

Instead, they establish a **Reverse Shell**.

Unlike a traditional web shell, a reverse shell provides an interactive terminal session.

The connection originates from the compromised server back to the attacker's machine.

```
Attacker
      ▲
      │
      │
Victim Server
```

This approach is advantageous because:

- Firewalls often allow outbound traffic.
- Interactive commands become possible.
- File transfers become easier.
- Enumeration becomes significantly faster.

Reverse shells are among the most common techniques used after successful web exploitation.

---

# Difference Between Web Shell and Reverse Shell

| Web Shell | Reverse Shell |
|------------|---------------|
| Executes one command at a time | Provides a fully interactive terminal |
| Uses HTTP requests | Uses a direct TCP connection |
| Slower | Faster |
| Leaves more HTTP artifacts | Produces continuous network traffic |
| Useful for initial access | Useful for post-exploitation |

Attackers frequently begin with a web shell before upgrading to a reverse shell for greater flexibility.

---

# Question 10

## Which port is used for the reverse shell connection?

### Answer

```
4422
```

---

# Identifying the Reverse Shell Port

Following the TCP stream after command execution reveals a new outbound connection initiated by the compromised host.

The destination port is:

```
4422
```

Instead of communicating through standard web ports such as **80** or **443**, the attacker selects a custom listening port.

This is typical of reverse shell payloads.

Analysts can identify these connections by looking for:

- New outbound TCP sessions.
- Unusual destination ports.
- Interactive command traffic.
- Continuous packet exchanges following exploitation.

These network characteristics strongly indicate successful remote access.

---

# Complete Attack Timeline

The packet capture allows us to reconstruct the attack from beginning to end.

```
Internal Host Reconnaissance
          │
          ▼
TCP SYN Port Scan
          │
          ▼
Service Discovery
          │
          ▼
Gobuster Enumeration
          │
          ▼
SQLMap Testing
          │
          ▼
File Upload
          │
          ▼
Web Shell Upload
          │
          ▼
Command Execution
          │
          ▼
"id" Command
          │
          ▼
Reverse Shell
          │
          ▼
Interactive Remote Access
```

This progression follows the typical stages of a real-world intrusion.

---

# MITRE ATT&CK Mapping

| Attack Stage | MITRE Technique |
|---------------|-----------------|
| Reconnaissance | T1595 – Active Scanning |
| Directory Enumeration | T1083 – File and Directory Discovery |
| SQL Injection Testing | T1190 – Exploit Public-Facing Application |
| Web Shell Upload | T1505.003 – Server Software Component: Web Shell |
| Command Execution | T1059 – Command and Scripting Interpreter |
| System Discovery | T1033 – System Owner/User Discovery |
| Reverse Shell | T1105 – Ingress Tool Transfer / Remote Access |
| Interactive Control | T1071 – Application Layer Protocol |

---

# Detection Opportunities

A Security Operations Center could identify this attack by monitoring:

- Internal hosts performing large-scale SYN scans.
- High volumes of failed connection attempts across multiple ports.
- Automated User-Agent strings such as **Gobuster** and **SQLMap**.
- HTTP POST requests containing uploaded PHP files.
- Execution of newly uploaded PHP scripts.
- HTTP parameters used to execute operating system commands.
- The `id` command executed by the web server process.
- Unexpected outbound TCP connections to uncommon ports such as **4422**.
- Long-lived TCP sessions originating from web servers after exploitation.

---

# Security Recommendations

Organizations can reduce the likelihood of similar attacks by implementing the following controls:

- Restrict unnecessary internal port scanning through network segmentation and monitoring.
- Deploy Intrusion Detection and Intrusion Prevention Systems (IDS/IPS) to detect reconnaissance activity.
- Harden file upload functionality by validating MIME types, file signatures, and storing uploads outside the web root.
- Disable execution of uploaded scripts within web-accessible directories.
- Implement a Web Application Firewall (WAF) to detect directory enumeration and SQL injection attempts.
- Monitor HTTP requests for suspicious parameters associated with command execution.
- Alert on unexpected outbound connections from web servers to non-standard ports.
- Perform continuous log review and network traffic analysis to identify attacker behavior early in the intrusion lifecycle.

---

# Lessons Learned

The **Network Analysis** challenge demonstrates how a complete intrusion can be reconstructed using only packet capture data. Beginning with internal reconnaissance, the attacker systematically scanned the target, enumerated web resources with automated tools, attempted SQL injection, uploaded a malicious PHP web shell, executed operating system commands, and ultimately established a reverse shell for interactive access.

This exercise highlights the importance of network visibility. Even without endpoint logs, defenders can identify each stage of the attack by analyzing protocols, HTTP requests, TCP conversations, and packet timing. Effective packet analysis enables SOC analysts to understand attacker methodology, validate alerts, and build a complete timeline of malicious activity.

---

# Conclusion

**Network Analysis** is an excellent introductory Blue Team Labs Online challenge for learning network forensics with Wireshark. It teaches analysts how to investigate suspicious network behavior, identify reconnaissance techniques, detect web application exploitation, and trace attacker actions from initial port scanning through successful remote access.

The challenge reinforces essential DFIR skills such as packet inspection, protocol analysis, HTTP investigation, attack timeline reconstruction, and MITRE ATT&CK mapping—making it a valuable exercise for aspiring SOC analysts, incident responders, and network defenders.
