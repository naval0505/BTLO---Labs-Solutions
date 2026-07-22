# Blue Team Labs Online - The Report Writeup

## Challenge Information

| Category | Details |
|----------|----------|
| Platform | Blue Team Labs Online (BTLO) |
| Challenge | The Report |
| Difficulty | Easy |
| Category | Threat Intelligence / SOC Operations / Defensive Security |

---

# Introduction

Today we are back with another **Blue Team Labs Online (BTLO)** defensive security challenge named **The Report**.

Unlike traditional Blue Team challenges that involve malware analysis, network forensics, or log investigation, this room focuses on understanding the operational side of a modern **Security Operations Center (SOC)**.

The challenge is based on the well-known **MITRE publication "11 Strategies of a World-Class Cybersecurity Operations Center"**, which discusses the design, operation, staffing, and continuous improvement of an effective SOC.

Instead of exploiting vulnerabilities or investigating incidents, we analyze this report and answer practical questions regarding SOC architecture, workflows, incident response, staffing models, and operational best practices.

Understanding these concepts is valuable for anyone pursuing careers in Security Operations, Threat Hunting, Incident Response, Digital Forensics, or Cyber Threat Intelligence.

---

# Scenario

The scenario provided for this challenge is:

> This challenge is an extension of the existing **The Report** challenge. You are working in a newly established Security Operations Center (SOC) where there is still significant work required to build a mature defensive capability. As part of improving the SOC, you have been assigned to study a report released by MITRE and identify key takeaways that can strengthen your organization's security operations.

The report referenced in this room is:

**11 Strategies of a World-Class Cybersecurity Operations Center**

published by **MITRE Corporation**.

---

# Objectives

The objectives of this challenge include:

- Study the MITRE SOC report.
- Understand different SOC organizational models.
- Learn SOC workflows.
- Identify SOC roles and responsibilities.
- Understand situational awareness.
- Learn incident response processes.
- Study remote SOC operations.
- Review SOC operational best practices.
- Apply the knowledge to answer practical questions.

---

# About the MITRE Report

MITRE is one of the most respected organizations in cybersecurity research.

The publication **11 Strategies of a World-Class Cybersecurity Operations Center** serves as a practical guide for organizations designing or improving their SOC capabilities.

Rather than focusing on specific technologies, the report discusses:

- SOC architecture
- Organizational structures
- Operational maturity
- Analyst responsibilities
- Detection engineering
- Threat intelligence integration
- Incident response workflows
- Continuous improvement

These recommendations are applicable to both small enterprise SOCs and national-level security operations centers.

---

# Preparing the Report

To begin the challenge, the report is downloaded onto the Kali Linux machine for offline review.

The document can either be downloaded directly from the MITRE website or opened using the official report link supplied within the BTLO challenge.

Once downloaded, the archive is extracted to reveal the PDF document used throughout the assessment.

Since the report exceeds hundreds of pages, manually reading every page would be time consuming.

Instead, targeted keyword searches using the PDF search function (`Ctrl + F`) allow rapid navigation to the relevant sections referenced by each question.

---

# Question 1

## Submit the name of the units or teams responsible for maintaining network infrastructure, incident detection and response, and security compliance.

### Answer

```
SOC, NOC, ISCM
```

---

# Understanding SOC, NOC and ISCM

The report explains that a mature cybersecurity program consists of multiple teams working together rather than a single Security Operations Center.

### Security Operations Center (SOC)

The SOC is responsible for:

- Security monitoring
- Threat detection
- Incident response
- Threat hunting
- Malware analysis
- Log analysis

Its primary responsibility is identifying and responding to security incidents.

---

### Network Operations Center (NOC)

The Network Operations Center manages:

- Routers
- Switches
- Firewalls
- Connectivity
- Network availability
- Infrastructure maintenance

While the SOC focuses on security events, the NOC ensures network reliability and operational uptime.

---

### Information Security Continuous Monitoring (ISCM)

ISCM continuously evaluates an organization's overall security posture by monitoring:

- Compliance
- Risk
- Vulnerability status
- Asset inventories
- Security controls

The report emphasizes that collaboration between SOC, NOC, and ISCM creates stronger organizational security.

---

# Question 2

## What are the four suggested Response Options mentioned in the Basic SOC Workflow?

### Answer

```
Block activity

Deactivate account

Continuous watching

Refer to outside party
```

---

# Understanding the Basic SOC Workflow

Every SOC follows a structured incident handling process.

Once an alert has been investigated, analysts decide which response is most appropriate.

The report identifies four primary response options.

### Block Activity

If malicious behavior is confirmed, security controls immediately prevent further attacker activity.

Examples include:

- Blocking IP addresses
- Blocking domains
- Firewall rules
- EDR isolation

---

### Deactivate Account

If a compromised account is involved, disabling user access prevents further unauthorized activity.

This is particularly important during credential theft incidents.

---

### Continuous Watching

Not every alert requires immediate containment.

Some suspicious activities require continuous monitoring until sufficient evidence exists.

Threat hunters frequently adopt this approach when investigating advanced attackers.

---

### Refer to Outside Party

Certain incidents require escalation beyond the SOC.

Examples include:

- Law enforcement
- Cloud providers
- Internet Service Providers
- Third-party vendors

The report highlights that escalation procedures should be clearly documented before incidents occur.

---

# Question 3

## What military strategy is used in SOCs to achieve high situational awareness?

### Answer

```
OODA Loop
```

---

# Understanding the OODA Loop

The **OODA Loop** is a military decision-making framework originally developed by Colonel John Boyd.

It consists of four repeating stages.

```
Observe

↓

Orient

↓

Decide

↓

Act
```

Unlike a linear workflow, the OODA Loop continuously repeats as new information becomes available.

---

### Observe

Collect information from:

- SIEM
- EDR
- Firewalls
- Threat intelligence
- Network sensors
- Endpoint logs

---

### Orient

Analysts evaluate the collected information.

This stage includes:

- Threat intelligence
- Historical incidents
- Environmental context
- Risk assessment

---

### Decide

The SOC determines the most appropriate response.

Possible decisions include:

- Monitoring
- Blocking
- Escalation
- Containment

---

### Act

Finally, the chosen action is implemented.

Examples include:

- Host isolation
- User suspension
- Firewall updates
- Incident response procedures

The faster a SOC cycles through the OODA Loop, the faster it can detect and respond to adversaries.

---

# Question 4

## What organizational model is recommended for organizations with approximately 1,000 to 10,000 employees?

### Answer

```
Distributed SOC
```

---

# Understanding the Distributed SOC Model

As organizations grow, a single centralized SOC often becomes difficult to manage.

The MITRE report recommends a **Distributed SOC** for medium-sized organizations.

Rather than placing every analyst in one location, responsibilities are distributed across multiple sites while maintaining centralized coordination.

Benefits include:

- Improved scalability
- Better regional coverage
- Reduced operational bottlenecks
- Increased resilience
- Faster incident handling

Distributed SOCs provide a balance between centralized management and local operational support.

---

# Question 5

## In a Large Centralized SOC, who is responsible for generating SOC metrics, maintaining situational awareness, and conducting internal and external training?

### Answer

```
SOC Lead
```

---

# Responsibilities of the SOC Lead

The SOC Lead serves as both a technical leader and an operational manager.

According to the MITRE report, responsibilities include:

- Managing SOC analysts
- Tracking operational metrics
- Maintaining situational awareness
- Coordinating major incidents
- Conducting internal training
- Organizing external exercises
- Improving detection capabilities
- Reporting SOC performance

The role ensures that analysts remain aligned with organizational objectives while continuously improving detection and response capabilities.

---

# Question 6

## In the Coordinating & National SOC model, what two functions are listed as optional capabilities under Expanded SOC Operations?

### Answer

```
Deception

Insider Threats
```

---

# Expanded SOC Operations

As SOCs mature, they often expand beyond traditional monitoring and incident response.

The report highlights additional optional capabilities that improve an organization's defensive posture.

---

### Deception

Deception technologies intentionally mislead attackers.

Examples include:

- Honeypots
- Honeytokens
- Fake credentials
- Decoy systems

These techniques increase attacker workload while providing defenders with valuable intelligence.

---

### Insider Threat Program

External attackers are not the only concern.

Insider threats may involve:

- Malicious employees
- Negligent users
- Compromised internal accounts

Dedicated insider threat programs combine behavioral monitoring, analytics, and investigations to detect suspicious internal activity.

---

# Question 7

## Which two virtual console technologies are recommended to support remote SOC operations?

### Answer

```
iLO

iDRAC
```

---

# Supporting Remote SOC Operations

Modern SOCs increasingly support analysts working remotely.

To maintain operational capability, administrators require secure methods of accessing infrastructure even when operating systems become unavailable.

The report specifically recommends two remote management technologies.

---

### Integrated Lights-Out (iLO)

Developed by Hewlett Packard Enterprise, **iLO** provides out-of-band management capabilities.

Administrators can:

- Access system consoles
- Reboot servers
- Monitor hardware
- Mount installation media

without requiring physical access.

---

### Integrated Dell Remote Access Controller (iDRAC)

Dell's equivalent technology is **iDRAC**.

It provides similar functionality, allowing administrators to manage servers remotely during maintenance or incident response activities.

These technologies proved especially valuable during large-scale remote work scenarios such as global pandemics.

---

# Question 8

## What model distributes SOC operations across different time zones to eliminate overnight shifts?

### Answer

```
Follow-the-Sun
```

---

# Understanding the Follow-the-Sun Model

A Security Operations Center must provide continuous monitoring throughout the day.

Instead of requiring analysts to work overnight, organizations can distribute operations across multiple geographic regions.

This model is known as the **Follow-the-Sun** approach.

For example:

```
Asia SOC

↓

Europe SOC

↓

North America SOC

↓

Asia SOC
```

As one regional team finishes its working day, responsibility transfers seamlessly to another location where business hours are beginning.

Benefits include:

- True 24/7 monitoring
- Reduced analyst fatigue
- Improved work-life balance
- Better incident response coverage
- Increased operational resilience

This model is commonly adopted by multinational organizations with globally distributed security teams.

---

# Part 1 Summary

The first section of the MITRE report focuses on building the operational foundation of a successful Security Operations Center. We explored how different teams such as the **SOC**, **NOC**, and **ISCM** collaborate, examined the basic incident response workflow, studied the **OODA Loop** for maintaining situational awareness, reviewed organizational models including the **Distributed SOC** and **Follow-the-Sun** approach, and learned about leadership responsibilities and expanded SOC capabilities such as **Deception** and **Insider Threat** programs.

In the next section, we will continue exploring incident prioritization, digital forensics support, cyber threat intelligence standards, large-scale data sources, endpoint telemetry retention, the **Pyramid of Pain**, and **Adversary Emulation** as described in the MITRE report.


# Question 9

## Submit the priorities assigned to the following incidents:

- Phishing
- Insider Threat
- Pre-Incident Port Scanning

### Answer

```
Medium

High

Low
```

---

# Understanding Incident Prioritization

One of the most important responsibilities of a Security Operations Center is determining which incidents require immediate attention.

Not every security event represents the same level of risk. Proper prioritization allows analysts to focus on incidents that have the greatest potential business impact.

The MITRE report categorizes incidents based on their severity, likelihood, and potential damage.

### Phishing — Medium

Phishing attacks remain one of the most common attack vectors.

Although many phishing emails are blocked before causing damage, successful phishing campaigns can lead to credential theft, malware infections, and initial access.

Because not every phishing attempt results in compromise, they are assigned a **Medium** priority.

---

### Insider Threat — High

Insider threats represent one of the most difficult attack scenarios for any organization.

Since insiders already possess legitimate access, traditional perimeter defenses often fail to detect malicious behavior.

Examples include:

- Data theft
- Sabotage
- Privilege abuse
- Unauthorized disclosure

Due to the potentially severe business impact, insider threats receive a **High** priority.

---

### Pre-Incident Port Scanning — Low

Attackers commonly perform reconnaissance before launching attacks.

Simple port scans generally indicate information gathering rather than an active compromise.

Although scanning should still be monitored, it is considered **Low** priority unless accompanied by additional malicious activity.

---

# Question 10

## What operating system is recommended for mobile incident investigations?

### Answer

```
Santoku
```

---

# Understanding Santoku Linux

The MITRE report references **Santoku Linux** as an operating system designed specifically for mobile security and forensic investigations.

Santoku is a Linux distribution that includes numerous tools for:

- Mobile device forensics
- Android analysis
- iOS investigations
- Malware analysis
- Application reverse engineering

It provides investigators with a specialized environment for analyzing smartphones and mobile applications during digital forensic investigations.

---

# Question 11

## Which two open threat intelligence standards should a Cyber Threat Intelligence (CTI) platform support?

### Answer

```
STIX

TAXII
```

---

# Understanding STIX and TAXII

Threat intelligence becomes significantly more valuable when it can be shared consistently between organizations.

The MITRE report recommends support for two industry-standard frameworks.

---

## STIX (Structured Threat Information eXpression)

STIX is a standardized language used to represent cyber threat intelligence.

It defines structured information such as:

- Indicators of Compromise (IOCs)
- Malware
- Threat actors
- Campaigns
- Attack patterns
- Relationships

Because the format is standardized, multiple security products can exchange intelligence without requiring custom data formats.

---

## TAXII (Trusted Automated eXchange of Intelligence Information)

While STIX defines how threat intelligence is represented, TAXII defines how that information is shared.

TAXII enables automated intelligence exchange between:

- Threat intelligence platforms
- SIEM solutions
- SOCs
- Government organizations
- Security vendors

Together, STIX and TAXII improve collaboration and accelerate threat detection across organizations.

---

# Question 12

## Which data source typically consumes the highest storage volume?

### Answer

```
PCAP
```

---

# Understanding PCAP Data

PCAP (Packet Capture) stores complete network packets exactly as they travel across the network.

Unlike logs, PCAP contains the full communication between systems.

Examples include:

- HTTP requests
- DNS queries
- SMB traffic
- SSH sessions
- TLS negotiations

Because every packet is recorded, storage requirements become extremely large.

The MITRE report notes that enterprise environments often generate **terabytes of PCAP data per day**, making it one of the largest data sources maintained by a SOC.

---

# Question 13

## What is the recommended retention period for Endpoint Detection and Response (EDR) logs?

### Answer

```
6
```

---

# Why EDR Data Retention Matters

Endpoint Detection and Response platforms continuously collect information from endpoints.

Examples include:

- Process execution
- Network connections
- Registry modifications
- File activity
- User logins
- Parent-child process relationships

The report recommends maintaining approximately **six months** of EDR telemetry.

Longer retention periods improve:

- Threat hunting
- Incident response
- Historical investigations
- Digital forensics

Analysts frequently investigate incidents weeks or months after the initial compromise, making long-term endpoint telemetry extremely valuable.

---

# Question 14

## According to the Pyramid of Pain, which indicators are Trivial, Easy, Challenging, and Tough for adversaries to change?

### Answer

```
Hash Values

IP Addresses

Tools

TTPs
```

---

# Understanding the Pyramid of Pain

The **Pyramid of Pain**, created by David Bianco, illustrates how difficult it is for attackers to modify different indicators after defenders detect them.

```
            TTPs
           /-----\
          / Tools \
         /---------\
        / Network  \
       / Artifacts  \
      /-------------\
     / Domain Names  \
    /-----------------\
   / IP Addresses      \
  /---------------------\
 / Hash Values           \
---------------------------
```

### Hash Values

Hashes are extremely easy for attackers to change.

Simply recompiling malware produces a completely different hash.

---

### IP Addresses

Attackers can rapidly replace compromised infrastructure or deploy new cloud servers.

Although slightly more difficult than changing hashes, changing IP addresses remains relatively simple.

---

### Tools

Replacing malware frameworks or offensive utilities requires considerably more effort.

Examples include:

- Cobalt Strike
- Metasploit
- Custom malware

Changing tools increases operational cost.

---

### Tactics, Techniques and Procedures (TTPs)

TTPs represent how adversaries operate.

These behaviors include:

- Initial access
- Persistence
- Credential access
- Lateral movement

Changing TTPs requires attackers to redesign entire operational methodologies.

Consequently, they represent the most valuable indicators for defenders.

---

# Question 15

## What Red Team approach attempts to replicate the Tactics, Techniques and Procedures of real adversaries?

### Answer

```
Adversary Emulation
```

---

# Understanding Adversary Emulation

Traditional penetration testing primarily focuses on discovering vulnerabilities.

Adversary emulation takes a different approach.

Instead of identifying every vulnerability, Red Teams attempt to imitate the behavior of real-world threat actors.

Examples include:

- APT29
- FIN7
- Lazarus Group
- Wizard Spider

Operations replicate:

- Initial access
- Persistence
- Privilege escalation
- Lateral movement
- Data exfiltration

By reproducing real attacker behavior, organizations can evaluate whether their SOC can detect genuine attack techniques rather than isolated exploits.

---

# Key Concepts Covered

Throughout this challenge, the MITRE report introduced several foundational concepts that contribute to building and operating an effective Security Operations Center.

These include:

- SOC organizational structures
- Incident response workflows
- Situational awareness using the OODA Loop
- Remote SOC operations
- Threat intelligence standards
- Digital forensics support
- Large-scale data management
- Threat hunting
- Detection engineering
- Adversary emulation

Together, these concepts form the operational backbone of mature defensive security programs.

---

# Detection Opportunities

Based on the recommendations within the MITRE report, organizations should focus on:

- Continuous monitoring of network and endpoint telemetry.
- Integrating Cyber Threat Intelligence into detection workflows.
- Leveraging STIX and TAXII for automated intelligence sharing.
- Maintaining long-term EDR and network packet retention for forensic investigations.
- Prioritizing high-impact incidents using structured triage methodologies.
- Monitoring attacker behaviors rather than relying solely on static indicators.
- Regularly conducting adversary emulation exercises to validate detection capabilities.

---

# Security Recommendations

To improve the maturity of a Security Operations Center, organizations should:

- Clearly define responsibilities across SOC, NOC, and ISCM teams.
- Establish standardized incident response procedures.
- Maintain continuous situational awareness through structured workflows.
- Implement scalable SOC operating models appropriate to organizational size.
- Enable secure remote administration capabilities for operational resilience.
- Invest in Cyber Threat Intelligence platforms supporting STIX and TAXII.
- Retain endpoint and network telemetry for effective forensic investigations.
- Focus detections on adversary TTPs instead of low-level indicators alone.
- Continuously measure and improve SOC performance through training and operational exercises.

---

# Lessons Learned

The **The Report** challenge demonstrates that building an effective Security Operations Center extends beyond deploying security tools. A mature SOC requires well-defined processes, skilled personnel, continuous monitoring, effective collaboration, and ongoing adaptation to evolving threats.

The MITRE report highlights the importance of combining operational strategy with technical capabilities, ensuring that organizations are prepared not only to detect attacks but also to respond efficiently, share intelligence, and continuously improve their defensive posture.

---

# Conclusion - Jai Shri Ram

**The Report** is an excellent Blue Team Labs Online challenge for anyone interested in Security Operations, Threat Intelligence, and SOC management. Rather than focusing on a single incident or malware sample, it introduces the broader operational concepts that underpin modern cybersecurity defense.

By studying the MITRE recommendations, defenders gain a deeper understanding of SOC workflows, organizational design, incident prioritization, threat intelligence integration, forensic readiness, and adversary-focused detection strategies. These principles provide a strong foundation for building resilient and effective Security Operations Centers capable of responding to today's evolving cyber threats.
