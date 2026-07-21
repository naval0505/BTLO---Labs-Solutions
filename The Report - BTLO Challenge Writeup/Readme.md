# Blue Team Labs Online - The Report-1 Writeup

![BTLO](images/cover.png)

## Basic Information

| Category | Details |
|----------|----------|
| Platform | Blue Team Labs Online (BTLO) |
| Challenge | The Report-1 |
| Difficulty | Easy |
| Category | Threat Intelligence / SOC / Defensive Security |
| Focus | Threat Report Analysis, MITRE ATT&CK, Vulnerability Intelligence, Detection Engineering |

---

# Introduction

Today we are solving another **Blue Team Labs Online (BTLO)** challenge named **The Report-1**.

Unlike traditional BTLO investigations where we analyze malware samples, network captures, or forensic artifacts, this challenge focuses on **Threat Intelligence**. The objective is to study a real-world threat report published in **2022**, understand the attack trends discussed within it, and answer questions based on the report.

For a Security Operations Center (SOC), threat reports are one of the most valuable intelligence sources. They help analysts understand how adversaries operate, what vulnerabilities are actively exploited, which malware families are currently dominant, and what defensive measures should be prioritized.

Throughout this challenge we review multiple attack campaigns from 2021 and 2022, map them to MITRE ATT&CK techniques, identify common exploitation trends, and extract actionable defensive recommendations.

---

# Scenario

The provided scenario states:

> You are working in a newly established Security Operations Center (SOC) where many defensive capabilities are still under development. As part of gathering threat intelligence, you have been assigned the task of studying a threat report released in 2022 and identifying useful outcomes that can strengthen the SOC's detection and response capabilities.

Unlike offensive security challenges, this exercise emphasizes understanding attacker behavior rather than exploiting systems.

---

# Objectives

During this assessment we aim to:

- Analyze a published threat intelligence report.
- Identify major cyber threats observed during 2021.
- Understand commonly exploited vulnerabilities.
- Map attacks to MITRE ATT&CK.
- Study ransomware trends.
- Review supply-chain attacks.
- Learn about Exchange Server compromises.
- Improve SOC detection capabilities.
- Produce defensive recommendations.

---

# Understanding Threat Intelligence Reports

Threat intelligence reports summarize attack campaigns observed across thousands of organizations.

These reports typically include:

- Emerging vulnerabilities
- Malware families
- Adversary tactics
- MITRE ATT&CK mappings
- Indicators of Compromise (IOCs)
- Detection opportunities
- Defensive recommendations

SOC analysts rely heavily on these reports to improve detection rules, prioritize patch management, and prepare for evolving attack techniques.

---

# Question 1

## Name the supply chain attack related to Java logging library in the end of 2021

### Answer

```
Log4Shell
```

---

# Understanding Log4Shell

One of the most significant cyber security events of 2021 was the discovery of the **Log4Shell** vulnerability affecting the **Apache Log4j** Java logging library.

Log4j is an open-source logging framework widely used by Java applications to record events, errors, and system activity. Because it was embedded in thousands of enterprise products and services, the vulnerability had an unprecedented global impact.

The flaw allowed attackers to trigger **Remote Code Execution (RCE)** by causing vulnerable systems to perform malicious JNDI lookups through specially crafted log entries. Simply logging attacker-controlled input—such as an HTTP header or user-agent string—could result in arbitrary code execution.

This made Log4Shell a classic **supply chain attack**, as the vulnerable component was a third-party library integrated into countless software products rather than a flaw in a single application.

### Why It Was Critical

- Remote Code Execution (RCE)
- Internet-wide exploitation within hours of disclosure
- Affected thousands of applications
- Impacted cloud services and enterprise software
- Required urgent patching across multiple industries

---

# Question 2

## Mention the MITRE ATT&CK Technique ID which affected more than 50% of the customers

### Answer

```
T1059
```

---

# MITRE ATT&CK Technique T1059

Technique **T1059** represents:

```
Command and Scripting Interpreter
```

Once attackers achieve code execution on a target system, they frequently use command interpreters such as:

- PowerShell
- Command Prompt
- Bash
- Python
- JavaScript
- Windows Script Host

to execute malicious commands, download payloads, establish persistence, and perform lateral movement.

Because command interpreters are legitimate administrative tools, detecting malicious usage requires behavioral monitoring rather than simple process detection.

### Why It Appears Frequently

T1059 consistently ranks among the most commonly observed ATT&CK techniques because it serves as a bridge between initial compromise and post-exploitation activities.

---

# Question 3

## Submit the names of two vulnerabilities affecting Microsoft Exchange Servers

### Answer

```
ProxyLogon

ProxyShell
```

---

# Understanding ProxyLogon

**ProxyLogon** refers to a chain of vulnerabilities affecting Microsoft Exchange Server that allowed attackers to bypass authentication and execute arbitrary code remotely.

The attack chain enabled adversaries to:

- Authenticate as an administrator.
- Upload malicious web shells.
- Execute commands remotely.
- Maintain persistence.
- Steal sensitive emails.

ProxyLogon quickly became one of the most exploited enterprise vulnerabilities worldwide, with thousands of internet-facing Exchange servers compromised before patches were widely deployed.

---

# Understanding ProxyShell

Following ProxyLogon, another major Exchange vulnerability known as **ProxyShell** emerged.

Although technically different, ProxyShell also enabled attackers to gain remote code execution against vulnerable Exchange servers.

Attackers frequently abused ProxyShell to:

- Deploy ransomware
- Install web shells
- Exfiltrate emails
- Establish persistence
- Perform lateral movement

The widespread exploitation of both ProxyLogon and ProxyShell demonstrated how critical timely patch management is for internet-facing infrastructure.

---

# Why Exchange Servers Became Prime Targets

Microsoft Exchange servers are attractive targets because they often store:

- Corporate email
- Internal communications
- User credentials
- Business documents
- Contact information

Compromising Exchange frequently provides attackers with both sensitive data and a foothold inside the enterprise network.

---

# Question 4

## Submit the CVE of the zero-day vulnerability involving a driver that led to Remote Code Execution and SYSTEM privileges

### Answer

```
CVE-2021-34527
```

---

# Understanding PrintNightmare

**CVE-2021-34527**, commonly known as **PrintNightmare**, affected the Windows Print Spooler service.

Although originally intended to manage printers, the Print Spooler service runs with elevated privileges on Windows systems.

The vulnerability allowed attackers to:

- Execute arbitrary code
- Load malicious printer drivers
- Escalate privileges
- Obtain SYSTEM-level access

Because the Print Spooler service is enabled by default on many Windows systems, exploitation became widespread shortly after public disclosure.

---

# Why PrintNightmare Was Dangerous

The vulnerability combined several high-impact characteristics:

- Remote exploitation
- Local privilege escalation
- SYSTEM privileges
- Domain compromise potential
- Broad deployment across enterprise environments

As a result, PrintNightmare became one of the most actively exploited Windows vulnerabilities of 2021.

---

# Key Takeaways

After analyzing the first section of the threat report, we identified several of the most impactful attack trends observed during 2021 and early 2022.

The report highlights how attackers increasingly relied on vulnerable third-party software, internet-facing enterprise services, and critical Windows components to gain initial access and execute malicious code.

So far we have successfully:

- Identified the **Log4Shell** supply chain attack.
- Reviewed the impact of the **Apache Log4j** vulnerability.
- Mapped malicious command execution to **MITRE ATT&CK T1059**.
- Studied the **ProxyLogon** and **ProxyShell** Exchange Server vulnerabilities.
- Examined the **PrintNightmare (CVE-2021-34527)** zero-day and its ability to grant SYSTEM-level privileges.

The next section of the report explores SEO poisoning campaigns, JavaScript malware execution, ransomware affiliate ecosystems, outdated enterprise software exploitation, DDoS extortion, and defensive measures such as Multi-Factor Authentication (MFA) for Remote Desktop Protocol (RDP) connections.


# Question 5

## Mention the two adversary groups that leverage SEO to gain initial access

### Answer

```
Gootkit

Yellow Cockatoo
```

---

# Understanding SEO Poisoning

Search Engine Optimization (SEO) poisoning is a technique where adversaries manipulate search engine rankings to place malicious websites near the top of legitimate search results.

Instead of attacking victims directly, attackers wait for users to search for topics such as:

- Software installation
- Business documents
- Contracts
- Legal templates
- Financial forms

When victims click the malicious search result, they unknowingly download malware disguised as legitimate content.

Unlike phishing emails, SEO poisoning exploits user trust in search engines, making it an increasingly effective initial access technique.

---

# Gootkit

One of the most well-known malware families using SEO poisoning is **Gootkit**.

Victims searching for business-related documents are redirected to compromised websites where JavaScript downloaders install malware.

Once executed, Gootkit typically deploys additional payloads such as ransomware or credential stealers.

Common objectives include:

- Credential theft
- Initial access
- Malware delivery
- Ransomware deployment

---

# Yellow Cockatoo

**Yellow Cockatoo**, also known as **SolarMarker**, is another threat group that extensively abused SEO poisoning.

Instead of phishing victims directly, the group manipulated search results to distribute malicious installers.

After infection, the malware established persistence, harvested credentials, and downloaded additional payloads.

Both Gootkit and Yellow Cockatoo demonstrate how attackers increasingly rely on legitimate web searches rather than email-based attacks.

---

# Question 6

## In the detection rule, what parent process should be monitored for malicious JavaScript execution?

### Answer

```
wscript.exe
```

---

# Why wscript.exe?

Windows Script Host (WSH) provides native support for executing scripting languages such as:

- JavaScript (.js)
- VBScript (.vbs)

The executable responsible for running these scripts is:

```
wscript.exe
```

Because Windows includes this utility by default, attackers frequently abuse it to execute malicious scripts without dropping custom executables.

---

# Why SOC Analysts Monitor wscript.exe

Malware campaigns commonly deliver JavaScript files through:

- Email attachments
- ZIP archives
- ISO images
- OneDrive links
- SEO poisoning

When opened, Windows launches:

```
wscript.exe
```

which executes the malicious JavaScript.

Monitoring unusual executions of **wscript.exe**, particularly when spawned by Office applications, browsers, or archive utilities, is an effective detection strategy.

---

# Question 7

## Name the precursors used by affiliates of the Conti ransomware group

### Answer

```
QBot

Bazar

IcedID
```

---

# Understanding the Conti Affiliate Model

Modern ransomware groups rarely compromise victims themselves.

Instead, they operate using an **affiliate model**.

```
Ransomware Developer

↓

Affiliate

↓

Victim Network

↓

Ransom Payment
```

Affiliates are responsible for obtaining initial access before deploying ransomware.

---

# QBot

QBot is a banking trojan that evolved into an initial access malware platform.

Capabilities include:

- Credential theft
- Browser data theft
- Lateral movement
- Payload delivery

Many Conti intrusions began with QBot infections.

---

# Bazar

BazarLoader focuses on establishing persistence and providing remote access.

Once installed, operators can deploy additional malware, including ransomware.

---

# IcedID

Originally developed as banking malware, IcedID later became a popular malware loader.

It frequently served as the first-stage infection before Conti ransomware deployment.

---

# Why Malware Loaders Matter

Rather than deploying ransomware immediately, attackers often:

```
Loader

↓

Credential Theft

↓

Privilege Escalation

↓

Lateral Movement

↓

Ransomware
```

This staged approach increases the likelihood of successfully compromising an entire enterprise.

---

# Question 8

## Mention the two outdated software platforms commonly exploited by coin miners

### Answer

```
JBoss

WebLogic
```

---

# Why Coin Miners Target Outdated Software

Cryptocurrency mining malware typically seeks easy opportunities rather than sophisticated attacks.

Internet-facing enterprise software that has not been patched provides an attractive target.

The report specifically highlights:

- JBoss
- WebLogic

Both platforms have experienced numerous critical remote code execution vulnerabilities over the years.

Attackers continuously scan the internet searching for unpatched instances.

---

# Importance of Patch Management

One of the strongest defensive measures against cryptomining malware is:

```
Regular Patch Management
```

Keeping enterprise applications updated significantly reduces exposure to publicly known vulnerabilities.

---

# Question 9

## Name the ransomware group that threatened victims with DDoS attacks if they refused to pay

### Answer

```
Fancy Lazarus
```

---

# Double Extortion Evolves

Traditional ransomware encrypted files and demanded payment.

Attackers later introduced:

- Data theft
- Public leak sites

The report describes another evolution.

Some groups threatened victims with **Distributed Denial-of-Service (DDoS)** attacks in addition to encryption.

One such actor was:

```
Fancy Lazarus
```

Although unrelated to the well-known **Lazarus Group** or **Fancy Bear**, this adversary attempted to pressure victims by threatening network disruption if ransom demands were ignored.

---

# Question 10

## What security measure should be enabled for RDP connections to reduce ransomware risk?

### Answer

```
MFA
```

---

# Why Multi-Factor Authentication Matters

Remote Desktop Protocol (RDP) remains one of the most abused services for ransomware intrusions.

Weak passwords, password spraying, and credential reuse frequently allow attackers to gain remote access.

Enabling:

```
Multi-Factor Authentication (MFA)
```

adds an additional verification step beyond passwords.

Even if credentials are stolen, attackers cannot authenticate without the second factor.

---

# Security Benefits of MFA

Benefits include:

- Prevents credential stuffing
- Blocks password reuse attacks
- Reduces brute-force success
- Protects remote administration
- Reduces ransomware risk

For internet-facing RDP systems, MFA should be considered mandatory.

---

# Complete Threat Landscape

```
Threat Report
       │
       ▼
Log4Shell
       │
       ▼
MITRE T1059
       │
       ▼
ProxyLogon
       │
       ▼
ProxyShell
       │
       ▼
PrintNightmare
       │
       ▼
SEO Poisoning
       │
       ▼
Gootkit
       │
       ▼
Yellow Cockatoo
       │
       ▼
Malicious JavaScript
       │
       ▼
wscript.exe
       │
       ▼
QBot
       │
       ▼
Bazar
       │
       ▼
IcedID
       │
       ▼
Coin Miner Campaigns
       │
       ▼
JBoss / WebLogic
       │
       ▼
Fancy Lazarus
       │
       ▼
RDP + MFA
```

---

# MITRE ATT&CK Mapping

| Technique | ATT&CK ID |
|-----------|-----------|
| Command and Scripting Interpreter | T1059 |
| Phishing / User Execution | T1204 |
| Exploit Public-Facing Application | T1190 |
| Valid Accounts | T1078 |
| External Remote Services | T1133 |
| Brute Force | T1110 |

---

# Detection Opportunities

A SOC should prioritize monitoring for:

- Exploitation attempts targeting Log4j, Exchange Server, and Print Spooler vulnerabilities.
- Unusual executions of `wscript.exe`, `cscript.exe`, and PowerShell.
- Indicators of SEO poisoning leading to malicious downloads.
- Malware families such as QBot, Bazar, and IcedID.
- Exploitation attempts against outdated JBoss and WebLogic servers.
- Suspicious RDP logins, especially those lacking MFA.
- Indicators of ransomware preparation, including credential dumping and lateral movement.

Threat intelligence feeds, EDR solutions, SIEM correlation rules, and continuous vulnerability management can significantly improve visibility into these attack techniques.

---

# Security Recommendations

To strengthen defensive capabilities:

- Maintain a robust vulnerability and patch management program.
- Prioritize remediation of critical internet-facing vulnerabilities.
- Deploy Multi-Factor Authentication (MFA) for all remote access services.
- Restrict unnecessary exposure of RDP.
- Monitor Windows Script Host (`wscript.exe`) for suspicious execution.
- Regularly review threat intelligence reports to update SOC detection rules.
- Conduct security awareness training covering phishing and SEO poisoning techniques.
- Continuously map detections to the MITRE ATT&CK framework.

---

# Lessons Learned

The **The Report-1** challenge demonstrates how threat intelligence can directly enhance a Security Operations Center's defensive posture. By analyzing real-world attack trends—including supply chain compromises, Exchange Server exploitation, ransomware affiliate ecosystems, SEO poisoning campaigns, and abused Windows components—SOC analysts can proactively improve detection logic, prioritize patching efforts, and implement stronger authentication controls.

Rather than focusing on a single incident, this challenge emphasizes the importance of understanding attacker behavior across multiple campaigns and translating that knowledge into practical defensive strategies.

---

# Conclusion

**The Report-1** from **Blue Team Labs Online** provides an excellent introduction to threat intelligence analysis and its role within a modern Security Operations Center. By reviewing major attack trends from 2021 and 2022, the challenge reinforces key concepts such as vulnerability management, MITRE ATT&CK mapping, malware delivery techniques, ransomware operations, and secure remote access practices.

For defenders, the biggest takeaway is that **timely threat intelligence, proactive patch management, continuous monitoring, and strong authentication controls form the foundation of an effective SOC capable of detecting and responding to evolving cyber threats.**
