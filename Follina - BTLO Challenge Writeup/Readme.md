# Follina - Blue Team Labs Online Writeup

## Basic Information

| Category | Details |
|----------|----------|
| Platform | Blue Team Labs Online (BTLO) |
| Challenge | Follina |
| Difficulty | Easy |
| Category | Malware Analysis / Threat Hunting |
| Malware Type | Malicious Microsoft Word Document |
| CVE | CVE-2022-30190 |

---

# Introduction

Today we are solving another **Blue Team Labs Online (BTLO)** challenge focused on **Malware Analysis** and **Threat Hunting**.

This challenge revolves around the infamous **Follina** vulnerability (**CVE-2022-30190**), a Microsoft Office Remote Code Execution vulnerability that gained significant attention in 2022 after being actively exploited in the wild before Microsoft released an official patch.

Rather than executing the malware sample, this investigation focuses on **static analysis**, **OpenXML document inspection**, **threat intelligence**, and **OSINT** to understand how the malicious document abuses Microsoft Word and the Microsoft Support Diagnostic Tool (**MSDT**) to execute arbitrary commands.

Throughout this investigation we will identify malicious artifacts, recover embedded URLs, analyze the document structure, correlate the sample with public intelligence, and understand the attacker's execution chain.

---

# Scenario

The provided scenario states:

> On a Friday evening when you were in a mood to celebrate your weekend, your team was alerted with a new Remote Code Execution (RCE) vulnerability actively being exploited.

As security analysts, our objective is to investigate the supplied Microsoft Word document and answer questions related to the exploit, associated indicators, and detection opportunities.

---

# Objectives

Throughout this investigation we aim to:

- Identify the malware sample.
- Verify file hashes.
- Correlate threat intelligence.
- Inspect the OpenXML document structure.
- Recover embedded URLs.
- Understand how Follina works.
- Identify execution artifacts.
- Map the malware to MITRE ATT&CK.

---

# Analysis Methodology

The investigation follows the workflow below.

```
Sample Acquisition
        │
        ▼
Hash Verification
        │
        ▼
Threat Intelligence
        │
        ▼
OpenXML Analysis
        │
        ▼
Relationship Inspection
        │
        ▼
Embedded URL Extraction
        │
        ▼
Exploit Analysis
        │
        ▼
Detection Mapping
```

---

# Sample Acquisition

The challenge provides a password-protected ZIP archive.

Password:

```
infected
```

Password-protected malware archives are commonly used by malware researchers to prevent antivirus software from automatically quarantining malicious samples.

The archive is extracted using:

```bash
unzip 43eecf22e8f914d44df3da16c23dcc2e076a8753.zip
```

Extraction reveals:

```
sample.doc
```

This Microsoft Word document becomes the primary artifact for investigation.

---

# Understanding OpenXML Documents

Modern Microsoft Office documents are not single binary files.

Instead, they are ZIP archives containing multiple XML files.

Their structure typically resembles:

```
Document

↓

ZIP Archive

↓

XML Files

↓

Relationships

↓

Media

↓

Configuration
```

Because of this design, investigators can analyze many malicious Office documents without opening them in Microsoft Word.

---

# Question 1

## Identify the SHA1 Hash

The first step is verifying the sample's integrity.

The SHA1 hash is calculated using:

```bash
sha1sum sample.doc
```

Output:

```text
06727ffda60359236a8029e0b3e8a0fd11c23313
```

### Answer

```
06727ffda60359236a8029e0b3e8a0fd11c23313
```

---

# Why Hashes Matter

Cryptographic hashes provide a unique fingerprint for files.

Analysts use them to:

- Identify malware.
- Search threat intelligence databases.
- Detect duplicate samples.
- Verify evidence integrity.

---

# Question 2

## Identify the File Type

Using the previously calculated SHA1 hash, the sample is searched on **VirusTotal**.

VirusTotal reports:

```
Office Open XML Document
```

### Answer

```
Office Open XML Document
```

---

# Additional Threat Intelligence

VirusTotal also provides valuable metadata including:

- MD5
- SHA256
- File size
- TrID identification
- Magic signature
- Detection ratio
- Community comments

Threat intelligence platforms frequently eliminate the need to execute malware directly.

---

# Understanding DOCX Files

Although the sample uses a **.doc** extension, the internal structure matches:

```
Office Open XML
```

This means the document consists of XML files packaged inside a ZIP container.

Rather than executing macros, Follina abuses the document relationship structure to reference external content.

---

# Question 3

## Extract the Embedded URL

Since Office Open XML files are ZIP archives, the document can be extracted directly.

```bash
unzip sample.doc
```

The extracted structure includes:

```
word

docProps

_rels

[Content_Types].xml
```

One particularly interesting file is:

```
word/_rels/document.xml.rels
```

Viewing the file reveals an external relationship.

```xml
Target="https://www.xmlformats.com/office/word/2022/wordprocessingDrawing/RDF842l.html!"
```

### Answer

```
https://www.xmlformats.com/office/word/2022/wordprocessingDrawing/RDF842l.html
```

---

# Why External Relationships Matter

Normally relationship files reference internal resources such as:

- Fonts
- Images
- Themes
- Styles

Instead, this document references an **external HTML resource**.

When the document is opened, Microsoft Word retrieves the remote content.

This external HTML page ultimately triggers the Follina exploit.

---

# Understanding Follina

Unlike traditional Office exploits that depend on malicious macros, Follina abuses Microsoft Word's ability to retrieve remote HTML content.

The attack flow is:

```
Word Document

↓

External HTML

↓

MSDT Protocol

↓

PowerShell

↓

Command Execution
```

Because macros are unnecessary, many traditional Office security controls fail to detect the attack.

---

# Question 4

## Identify the XML File

The extracted URL is stored inside:

```
document.xml.rels
```

located under:

```
word/_rels/
```

### Answer

```
document.xml.rels
```

---

# Understanding Relationship Files

Relationship files define how different components inside an Office document connect together.

Examples include:

- Images
- Hyperlinks
- Themes
- Fonts
- External Objects

During malware analysis, these files are often the quickest place to discover:

- External URLs
- OLE Objects
- Remote Templates
- Exploit chains

---

# Question 5

## HTML Processing Threshold

Research into the Follina exploit reveals an interesting implementation detail.

The malicious HTML payload must exceed a minimum size before Microsoft processes it correctly.

Public technical analysis documents this minimum threshold as:

```
4096 bytes
```

### Answer

```
4096
```

---

# Why File Size Matters

The exploit abuses Microsoft's HTML processing behavior.

Smaller HTML files fail to trigger the vulnerable parsing logic.

Attackers therefore intentionally pad their HTML payloads until they exceed the required size.

This characteristic became one of the early Indicators of Compromise used by security researchers during the initial Follina investigations.

---

# Current Investigation Findings

At this stage we have successfully:

- Extracted the protected malware sample.
- Verified the SHA1 hash.
- Correlated the sample using VirusTotal.
- Identified the document as an Office Open XML file.
- Extracted the embedded external URL.
- Located the relationship file responsible for loading remote content.
- Understood how Follina leverages external HTML resources.
- Identified the HTML size requirement used during exploitation.

The next phase focuses on understanding the MSDT execution chain, process creation artifacts, Windows Event ID 4688 detection opportunities, MITRE ATT&CK mapping, Indicators of Compromise (IOCs), and defensive recommendations for detecting and mitigating Follina-based attacks.

# Question 6

## Identify the Process Terminated by the Malware

After successfully triggering the exploit, the malicious HTML payload launches the **Microsoft Support Diagnostic Tool (MSDT)** to execute attacker-controlled commands.

Public analyses of Follina reveal that many samples terminate the diagnostic process after execution to reduce visible artifacts and minimize user suspicion.

The process targeted is:

```
msdt.exe
```

### Answer

```
msdt.exe
```

---

# Why Terminate MSDT?

The Microsoft Support Diagnostic Tool is normally launched to troubleshoot Windows problems.

Once the exploit completes its execution, leaving MSDT open could:

- Alert the victim.
- Reveal unexpected activity.
- Increase the chances of detection.

Terminating the process helps the malware remain less noticeable.

---

# Understanding the Follina Execution Chain

Unlike macro-based attacks, Follina abuses Microsoft's **MSDT URI protocol**.

The overall execution flow appears as follows.

```
Malicious Word Document
          │
          ▼
External HTML Payload
          │
          ▼
MSDT URI Protocol
          │
          ▼
msdt.exe
          │
          ▼
PowerShell
          │
          ▼
Arbitrary Command Execution
```

Because no VBA macros are required, many traditional Office protections are bypassed.

---

# Question 7

## Windows Event ID 4688 Detection

One of the most valuable Windows forensic artifacts is:

```
Event ID 4688

Process Creation
```

During exploitation, Microsoft Word launches MSDT.

This creates a highly recognizable parent-child process relationship.

```
Parent Process

↓

WINWORD.EXE

↓

Child Process

↓

MSDT.EXE
```

### Answer

```
msdt.exe, winword.exe
```

---

# Why Event ID 4688 Matters

Windows Event ID 4688 records:

- Process name.
- Parent process.
- Command line.
- User.
- Process ID.

This makes it extremely valuable for detecting exploitation attempts.

Normally Microsoft Word should **not** spawn:

- msdt.exe
- powershell.exe
- cmd.exe

Any such behavior should be treated as highly suspicious.

---

# Example Detection Logic

A simple process creation rule may resemble:

```
Parent Process

WINWORD.EXE

AND

Child Process

MSDT.EXE
```

Security products frequently use this relationship to detect Follina exploitation.

---

# Question 8

## MITRE ATT&CK Technique

Uploading the sample to VirusTotal provides additional behavioral analysis.

Reviewing the **MITRE ATT&CK** section reveals several mapped techniques.

The primary execution technique used by the exploit is:

```
T1203

Exploitation for Client Execution
```

### Answer

```
T1203
```

---

# Why This Technique Applies

The victim must open the malicious Microsoft Word document.

The exploit then abuses a vulnerability within the client application to execute attacker-controlled code.

This behavior perfectly matches:

```
Exploitation for Client Execution
```

within the MITRE ATT&CK framework.

---

# Question 9

## Associated CVE

The exploit analyzed throughout this challenge is publicly tracked as:

```
CVE-2022-30190
```

commonly known as:

```
Follina
```

### Answer

```
CVE-2022-30190
```

---

# About Follina

Follina became one of the most widely discussed Microsoft Office vulnerabilities because it allowed attackers to execute arbitrary commands without requiring:

- VBA macros.
- Protected View bypasses.
- User interaction beyond opening the document.

This significantly increased the attack surface for phishing campaigns.

Microsoft later released security updates to address the vulnerability.

---

# Indicators of Compromise (IOCs)

| Indicator | Value |
|-----------|-------|
| SHA1 | 06727ffda60359236a8029e0b3e8a0fd11c23313 |
| File Type | Office Open XML Document |
| External URL | https://www.xmlformats.com/office/word/2022/wordprocessingDrawing/RDF842l.html |
| XML File | document.xml.rels |
| Exploited Process | msdt.exe |
| Parent Process | winword.exe |
| CVE | CVE-2022-30190 |

---

# MITRE ATT&CK Mapping

| Tactic | Technique | ATT&CK ID |
|---------|-----------|-----------|
| Initial Access | Phishing Attachment | T1566.001 |
| Execution | Exploitation for Client Execution | T1203 |
| Execution | Command and Scripting Interpreter (PowerShell) | T1059.001 |
| Execution | Inter-Process Communication | T1559 |
| Defense Evasion | Masquerading via Trusted Windows Components | T1036 |

---

# Detection Opportunities

Security teams should monitor for:

- Microsoft Word spawning `msdt.exe`.
- `WINWORD.EXE` creating PowerShell or Command Prompt processes.
- Office documents containing external relationship URLs.
- Documents referencing remote HTML files inside `document.xml.rels`.
- Network requests to suspicious HTML resources immediately after document execution.
- Microsoft Office accessing MSDT protocol handlers.
- Unusual process trees beginning with Microsoft Office applications.

Combining endpoint telemetry with Windows Event ID 4688 and network monitoring provides excellent visibility into Follina exploitation attempts.

---

# YARA Detection Ideas

Static detection can focus on several characteristics commonly observed in Follina documents.

Examples include:

- External relationship entries.
- MSDT protocol references.
- Suspicious OLE relationships.
- Remote HTML objects.
- Abnormal Office relationship identifiers.

Although no single signature guarantees detection, combining multiple indicators significantly improves detection accuracy.

---

# Sigma Detection Ideas

Potential Sigma rules may alert on:

- `WINWORD.EXE` spawning `MSDT.EXE`.
- Office applications launching PowerShell.
- Microsoft Office executing diagnostic utilities.
- Child processes originating from Office without user interaction.

Behavior-based detections remain more resilient than hash-based signatures because attackers can easily modify document contents while preserving the exploitation technique.

---

# Security Recommendations

To defend against Follina-style attacks, organizations should:

- Apply Microsoft's security updates for CVE-2022-30190.
- Disable the MSDT URL protocol where appropriate.
- Block Office applications from spawning child processes.
- Enable Windows process creation logging (Event ID 4688).
- Deploy Endpoint Detection and Response (EDR) solutions.
- Filter phishing attachments before delivery.
- Restrict outbound connections from Microsoft Office applications.
- Educate users about malicious Office documents and phishing campaigns.

---

# Lessons Learned

The **Follina** challenge demonstrates how modern Office document exploits no longer rely exclusively on malicious macros. Instead, attackers abused legitimate Microsoft functionality by embedding external relationships inside an OpenXML document, causing Microsoft Word to retrieve a remote HTML payload that leveraged the MSDT protocol to execute arbitrary commands.

The investigation also highlights the importance of static malware analysis. Without executing the sample, we successfully extracted embedded URLs, identified the exploit chain, correlated threat intelligence, mapped the behavior to MITRE ATT&CK, and identified valuable detection opportunities. This reinforces the value of understanding document internals, public threat intelligence, and Windows telemetry when responding to emerging vulnerabilities.

---

# Conclusion - Jai Jagannath

The **Follina** BTLO challenge provides an excellent introduction to document-based malware analysis and threat hunting. Through static inspection of a malicious Microsoft Word document, we identified the embedded external relationship responsible for triggering the exploit, correlated the sample with public threat intelligence, and examined how **CVE-2022-30190** abuses the Microsoft Support Diagnostic Tool (**MSDT**) to achieve Remote Code Execution.

Beyond identifying the exploit, the investigation explored practical detection opportunities, including Windows Event ID 4688 process creation logs, suspicious Office process relationships, and MITRE ATT&CK mappings. Overall, this challenge demonstrates how analysts can investigate modern Office exploits without executing malware, making it an excellent exercise for developing malware analysis, threat hunting, and defensive security skills.
