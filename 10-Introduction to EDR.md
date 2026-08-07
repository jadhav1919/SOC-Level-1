# Endpoint Detection and Response (EDR)

# Why Do We Need EDR?

Today, employees work from:

* Office
* Home (Remote Work)
* Coffee shops
* Hotels

Traditional security (like a firewall) mainly protects devices **inside the company's network**.

When employees work remotely, their laptops are **outside the company's network**, making them more vulnerable to cyber attacks.

**EDR solves this problem** by protecting and monitoring endpoints wherever they are.

# What is EDR?

**EDR (Endpoint Detection and Response)** is a security solution that continuously monitors endpoint devices to detect, investigate, and respond to cyber threats.

> **Endpoint (A device connected to a network)** examples:
>
> * Laptop
> * Desktop
> * Server
> * Virtual Machine (VM)

Unlike traditional antivirus, EDR not only detects malware but also helps analysts investigate and respond to attacks.

# Popular EDR Solutions

Some widely used EDR products are:

* CrowdStrike Falcon
* Microsoft Defender for Endpoint
* SentinelOne ActiveEDR
* Symantec EDR
* OpenEDR

Although features vary, most EDR solutions follow the same basic architecture.


# The 3 Pillars of EDR

Every EDR has **three main features (pillars)**:

```
EDR
│
├── Visibility
├── Detection
└── Response
```

# 1. Visibility

## What is Visibility?

**Visibility** means the EDR continuously collects detailed information from endpoint devices so analysts can see exactly what happened.

Think of it as a **security camera for your computer**.

Instead of just saying:

> "Malware detected"

The EDR shows:

* Which process started.
* Which file was opened.
* Which registry key was modified.
* Which network connection was made.
* Which user performed the action.
* The complete timeline of events.

## What Information Does EDR Collect?

### Process Modifications

**Process (A running program)**

Examples:

* chrome.exe
* cmd.exe
* powershell.exe
* explorer.exe

The EDR records:

* Which process started.
* Which process created another process.
* Parent-child process relationships.

### Registry Modifications

**Registry (Windows database storing system and application settings)**

EDR detects:

* Registry keys created.
* Registry keys modified.
* Registry keys deleted.

Useful for detecting malware persistence.

### File & Folder Modifications

EDR monitors:

* File creation.
* File deletion.
* File modification.
* File renaming.

Example:

```text
invoice.docx

↓

invoice.docx.exe
```

### User Actions

Examples:

* User logins.
* Logoffs.
* USB inserted.
* PowerShell executed.
* Command Prompt opened.

### Network Connections

EDR records:

* Which process connected to the Internet.
* Destination IP.
* Destination Domain.
* Port Number.

Example:

```text
powershell.exe

↓

45.12.76.90:443
```

# Process Tree

One of the most useful EDR features is the **Process Tree**.

## What is a Process Tree?

A **Process Tree** shows the relationship between processes.

Example:

```text
explorer.exe
      │
      ▼
cmd.exe
      │
      ▼
powershell.exe
      │
      ▼
malware.exe
```

This helps analysts understand **how the attack started**.

# Why is Visibility Important?

Without EDR:

 You only know malware was detected.

With EDR:

 You know:

* Who started it.
* When it started.
* Which files changed.
* Which registry keys changed.
* Which network connections were made.
* Which child processes were created.

# 2. Detection

## What is Detection?

Detection means identifying suspicious or malicious activity on the endpoint.

Unlike traditional antivirus, EDR uses multiple detection methods.

## Detection Methods

### Signature-Based Detection

Detects known malware using signatures (digital fingerprints).

Example:

Known ransomware file detected.

### Behavior-Based Detection

Looks for suspicious behavior instead of specific files.

Example:

PowerShell downloads malware.

↓

Even if the malware is new, the behavior is suspicious.


### Machine Learning (ML)

**Machine Learning (AI that learns patterns from data)**

EDR compares current activity with normal behavior.

If behavior suddenly changes, it generates an alert.

Example:

A user normally logs in from India.

↓

Suddenly logs in from Russia.

↓

EDR flags unusual behavior.


### Fileless Malware Detection

**Fileless Malware (Malware that runs in memory without creating files on disk)**

Traditional antivirus may miss it.

EDR monitors memory activity and suspicious behavior.

### Custom IOCs

**IOC (Indicator of Compromise – evidence that a system may be compromised)**

Examples:

* Malicious IP address
* Malicious domain
* File hash
* Registry key

SOC teams can add custom IOCs to detect specific threats.

# Detection Dashboard

The Detection Dashboard displays:

* Alert Name
* Severity
* Time
* Hostname
* Username
* File Name
* MITRE ATT&CK Technique

This gives analysts enough context to investigate quickly.


# 3. Response

## What is Response?

Response means taking action to stop or contain the threat.

The analyst can perform these actions directly from the EDR console.

## Common Response Actions

### Isolate Host

Disconnect the infected computer from the network.

Purpose:

Prevent malware from spreading.

### Terminate Process

Stop a malicious process.

Example:

```text
malware.exe

↓

Terminate
```


### Quarantine File

Move the malicious file to a secure location so it cannot execute.


### Remote Investigation

Some EDRs allow analysts to connect remotely to the endpoint.

Example:

Run commands.

Collect logs.

View files.

Without physically visiting the computer.


### Real-Time Response (RTR)

**RTR (Real-Time Response)** allows analysts to investigate and respond immediately from the EDR console.

Example actions:

* Run commands.
* Kill processes.
* Delete files.
* Collect forensic evidence.


# Why is EDR Powerful?

EDR provides:

* High visibility.
* Advanced detection.
* Fast response.

This helps SOC analysts investigate incidents much faster.


# Limitation of EDR

EDR focuses only on **endpoint devices (hosts)**.

It **does not monitor network-level threats** by itself.

For complete visibility, organizations also use:

* SIEM
* NDR (Network Detection and Response)
* Firewalls
* IDS/IPS


# Summary Table

| Pillar         | Purpose                     | Example                                          |
| -------------- | --------------------------- | ------------------------------------------------ |
| **Visibility** | Collect endpoint activity   | Process tree, registry changes, file changes     |
| **Detection**  | Identify malicious activity | Malware detection, suspicious behavior, ML       |
| **Response**   | Stop or contain threats     | Isolate host, terminate process, quarantine file |

--------------------

# Antivirus (AV) vs Endpoint Detection & Response (EDR) 

# Why Do We Need EDR If We Already Have Antivirus?

Both **Antivirus (AV)** and **EDR (Endpoint Detection and Response)** protect endpoint devices, but they work differently.

Think of it like **airport security**.


# Airport Example

### Antivirus (AV)

Imagine an **Immigration Officer** at an airport.

The officer checks:

* Passport
* Identity
* Criminal database

If the person's name matches a known criminal,

 Entry is blocked.

If the person is unknown,

They are allowed inside.

**AV works the same way.**

It checks files against a database of **known malware signatures (unique digital fingerprints of known malware).**

If a match is found,

→ Malware is blocked.

If there is **no known signature**,

→ The file is allowed.

### EDR

Now imagine **Security Officers** inside the airport.

They constantly monitor:

* CCTV cameras
* People's behaviour
* Suspicious activities

Examples:

* Walking into restricted areas.
* Leaving bags unattended.
* Acting suspiciously.

Even if someone entered legally, the security officers can still detect suspicious behaviour and stop them.

**EDR works the same way.**

Instead of only checking files,

it continuously monitors **everything happening on the endpoint.**

# Antivirus vs EDR

| Antivirus (AV)                                          | EDR                                                    |
| ------------------------------------------------------- | ------------------------------------------------------ |
| Checks known malware signatures (digital fingerprints). | Continuously monitors endpoint behaviour.              |
| Detects mostly known threats.                           | Detects both known and unknown threats.                |
| Limited visibility.                                     | Full endpoint visibility.                              |
| Mostly prevents malware execution.                      | Detects, investigates, and responds to attacks.        |
| Works on one endpoint.                                  | Can investigate all endpoints across the organization. |


# Real Attack Scenario

Let's compare how AV and EDR behave during a phishing attack.

## Step 1

### User receives a phishing email with a malicious Word document.

Example:

```text
Invoice.docm
```

(Contains a malicious macro.)

### Antivirus

* Checks the file signature.
* If the malware is unknown,

Allows the download.

### EDR

* Logs the download.
* Starts monitoring the file.

## Step 2

### User opens the Word document.

```text
winword.exe
```

starts.

### Antivirus

Word is a legitimate application.

 No alert.

### EDR

Records:

* User opened the document.
* winword.exe started.
* Keeps monitoring.

## Step 3

### The document executes a Macro.

**Macro (Small program inside Microsoft Office documents that automates tasks.)**

The macro starts:

```text
powershell.exe
```

### Antivirus

Unknown macro.

 No detection.

### EDR

Detects something unusual.

Normally:

```text
explorer.exe

↓

winword.exe
```

Not:

```text
winword.exe

↓

powershell.exe
```

This unusual **Parent-Child Process Relationship (which process started another process)** is suspicious.

 Alert generated.

---

## Step 4

### PowerShell downloads malware.

The command is **Obfuscated (hidden or intentionally made difficult to read).**

Example:

```text
PowerShell -EncodedCommand ...
```

### Antivirus

Cannot understand the hidden script.

✔ No alert.

### EDR

Detects:

* Obfuscated PowerShell.
* Suspicious download behaviour.

🚨 Alert generated.

---

## Step 5

### Malware injects itself into

```text
svchost.exe
```

**Process Injection (malicious code inserted into another legitimate process).**

### Antivirus

Usually cannot detect memory injection.

✔ No alert.

### EDR

Detects:

* Memory injection.
* Process Injection.

🚨 Alert generated.

---

## Step 6

### The attacker gains remote access.

The malware now communicates with an external server.

### Antivirus

Limited visibility.

May miss the network activity.

### EDR

Detects:

* Unexpected outbound network connection.
* Suspicious behaviour from svchost.exe.

🚨 Alert generated.

---

# Final Result

### Antivirus

May report:

```text
No Threat Found
```

because every individual step looked legitimate.

---

### EDR

Builds the **complete attack chain**.

Example:

```text
Email

↓

Word Document

↓

Macro

↓

PowerShell

↓

Malware Download

↓

Process Injection

↓

Remote Connection
```

The analyst sees the entire attack from beginning to end.

---

# Why is EDR Better?

EDR doesn't only check **files**.

It monitors:

* Process execution.
* Parent-child relationships.
* PowerShell activity.
* Registry changes.
* File modifications.
* Memory activity.
* Network connections.
* User behaviour.

This allows it to detect attacks that traditional antivirus may miss.

-------------------

# How Does EDR Work? 

# How Does EDR Work?

An EDR works using **two main components**:

```text
               EDR
                │
      ┌─────────┴─────────┐
      │                   │
EDR Agent (Sensor)    EDR Console
```

* **EDR Agent (Sensor)** → Installed on every endpoint (computer, laptop, server).
* **EDR Console** → Central dashboard where analysts monitor and manage all endpoints.

---

# 1. EDR Agent (Sensor)

## What is an EDR Agent?

An **EDR Agent (small software installed on an endpoint)** continuously monitors everything happening on that device.

Think of it as the **eyes and ears** of the EDR.

---

## Where is it Installed?

On every endpoint, such as:

* Laptop
* Desktop
* Server
* Virtual Machine (VM)

---

## What Does the Agent Monitor?

The agent collects information such as:

* Process execution
* File creation/modification
* Registry changes
* Network connections
* User logins
* USB activity
* PowerShell execution
* Command Prompt activity
* And much more

---

## What Does the Agent Do?

The agent:

1. Monitors endpoint activity.
2. Collects detailed logs.
3. Performs basic **Signature-Based Detection (detecting known malware using signatures)**.
4. Performs basic **Behavior-Based Detection (detecting suspicious activities based on behavior)**.
5. Sends all collected information to the EDR Console in **real time**.

---

# Example

A user runs:

```text
powershell.exe
```

The EDR Agent records:

* User
* Time
* Hostname
* Parent Process
* Child Process
* Network Connections
* Registry Changes
* File Changes

Then sends all this information to the EDR Console.

---

# 2. EDR Console

## What is an EDR Console?

The **EDR Console (central dashboard used to manage all endpoints)** is the "brain" of the EDR.

It receives data from all endpoint agents.

---

## What Does the Console Do?

The console:

* Receives logs from all endpoints.
* Correlates (connects related events together) events.
* Analyzes suspicious activities.
* Uses **Machine Learning (AI that learns patterns from data)**.
* Uses **Threat Intelligence (database of known malicious IPs, domains, hashes, etc.)**.
* Generates alerts.

---

# Example

The Console receives:

```text
Laptop A

↓

Word document opened

↓

PowerShell started

↓

Downloaded malware

↓

Connected to malicious IP
```

The console links all these events together.

Instead of showing four separate events,

it creates **one security alert**.

---

# Detection Flow

```text
User opens Word document
          │
          ▼
EDR Agent collects activity
          │
          ▼
Data sent to EDR Console
          │
          ▼
Console analyzes activity
          │
          ▼
Threat Intelligence + Machine Learning
          │
          ▼
Alert Generated
```

---

# What Happens After Detection?

Once an alert is generated, the SOC analyst takes over.

---

## Step 1 – Alert Appears

The EDR Console creates a new alert.

---

## Step 2 – Assign Severity

The EDR automatically assigns a severity level.

Common severity levels:

*  Critical
*  High
*  Medium
*  Low
*  Informational

Analysts investigate **Critical** alerts first.

---

## Step 3 – Investigation

The analyst opens the alert.

The EDR provides details like:

* Process Tree
* Executed files
* Network connections
* Registry changes
* File modifications
* User information
* Timeline
* MITRE ATT&CK mapping

---

## Step 4 – Make a Verdict

The analyst decides:

* **False Positive (normal activity)**

OR

* **True Positive (real security threat)**

---

## Step 5 – Response

If it is a **True Positive**, the analyst can respond directly from the EDR.

Common actions include:

* Isolate Host
* Kill malicious process
* Quarantine malicious file
* Collect forensic evidence
* Run remote commands

---

# Investigation Workflow

```text
EDR Agent
      │
      ▼
Collect Endpoint Activity
      │
      ▼
Send Logs
      │
      ▼
EDR Console
      │
      ▼
Analyze Events
      │
      ▼
Generate Alert
      │
      ▼
SOC Analyst
      │
      ▼
Investigate
      │
      ▼
True Positive?
      │
 ┌────┴─────┐
 │          │
No         Yes
 │           │
Close     Respond
```

---

# EDR with Other Security Tools

An EDR does **not work alone**.

It is one part of a larger security ecosystem.

Common security tools include:

| Tool                                                                                                                                              | Purpose                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| **Firewall**                                                                                                                                      | Filters network traffic.                                  |
| **DLP (Data Loss Prevention – prevents sensitive data from leaving the organization)**                                                            | Protects sensitive information from unauthorized sharing. |
| **Email Security Gateway**                                                                                                                        | Blocks phishing emails, spam, and malicious attachments.  |
| **IAM (Identity and Access Management – manages user identities and access permissions)**                                                         | Controls authentication and user access.                  |
| **EDR**                                                                                                                                           | Monitors and protects endpoint devices.                   |
| **SIEM (Security Information and Event Management – central platform that collects, correlates, and analyzes security logs from multiple tools)** | Central investigation platform for SOC analysts.          |

---

# Security Ecosystem

```text
Firewall
     │
Email Gateway
     │
IAM
     │
EDR
     │
DLP
     │
───────────────
      SIEM
───────────────
      │
SOC Analysts
```

All these security tools send their logs to the **SIEM**, which becomes the **central investigation platform** for the SOC team.

---

# Why Integrate EDR with SIEM?

Without SIEM:

* Analysts must check each security tool separately.

With SIEM:

* Logs from all tools are collected in one place.
* Events from different tools are correlated.
* Investigations become faster and easier.

--------------------

# Telemetry in EDR 


# What is Telemetry?

**Telemetry** is the detailed data collected by an **EDR Agent (software installed on an endpoint)** about everything happening on an endpoint.

Think of Telemetry as the **Black Box (like an airplane's flight recorder)** of a computer.

Just as an airplane's black box records everything before an accident, EDR telemetry records everything happening on an endpoint before, during, and after a cyber attack.

---

# Why is Telemetry Important?

EDR uses telemetry to:

* Detect suspicious activities.
* Investigate security incidents.
* Reconstruct the attack timeline.
* Find the root cause (main reason) of an attack.

The more telemetry collected, the easier it is to distinguish **normal activity** from **malicious activity**.

---

# Types of Telemetry Collected by EDR

## 1. Process Execution and Termination

### What is it?

EDR records every process (running program) that starts and stops.

### It monitors:

* Process creation
* Process termination
* Parent process
* Child process

### Why is it important?

Helps detect:

* Malware execution
* Suspicious parent-child process relationships
* Malicious executables

### Example

```text
explorer.exe
      │
      ▼
winword.exe
      │
      ▼
powershell.exe
```

Normally, **Word should not launch PowerShell**. This unusual process chain can indicate malware.

---

## 2. Network Connections

### What is it?

EDR records every network connection made by the endpoint.

### It monitors:

* Source IP
* Destination IP
* Port Number
* Domain Name
* Protocol

### Why is it important?

Helps detect:

* **C2 (Command and Control - attacker's remote server)** communication.
* Data Exfiltration (stealing data from the organization).
* Lateral Movement (attacker moving to other systems inside the network).
* Unusual network traffic.

### Example

```text
powershell.exe

↓

45.76.120.10:443
```

PowerShell connecting to an unknown external IP is suspicious.

---

## 3. Command Line Activity

### What is it?

EDR records commands executed in:

* CMD (Command Prompt)
* PowerShell
* Terminal

### Why is it important?

Helps detect:

* Malicious commands
* Encoded PowerShell
* Obfuscated Scripts (scripts intentionally made difficult to read)
* Privilege escalation attempts

### Example

```text
powershell.exe -EncodedCommand ...
```

Encoded PowerShell commands are commonly used by attackers.

---

## 4. File and Folder Modifications

### What is it?

EDR records all file and folder changes.

### It monitors:

* File creation
* File deletion
* File modification
* File renaming
* Folder creation

### Why is it important?

Helps detect:

* Malware dropping files
* Ransomware encrypting files
* Data staging (collecting data before stealing it)

### Example

```text
EmployeeData.xlsx

↓

EmployeeData.xlsx.locked
```

This could indicate ransomware encryption.

---

## 5. Registry Modifications

### What is the Registry?

The **Windows Registry (database that stores Windows settings and configurations)**.

Many malware families modify the registry.

### EDR monitors:

* Registry key creation
* Registry key modification
* Registry key deletion

### Why is it important?

Helps detect:

* Malware persistence (malware making itself start automatically after reboot).
* Security setting changes.
* Suspicious configuration changes.

### Example

Malware creates a registry key so it runs every time Windows starts.

EDR records the registry change.

---

# How EDR Uses Telemetry

EDR does not look at one event alone.

Instead, it combines multiple telemetry events.

Example:

```text
Word Document Opened
        │
        ▼
Macro Executed
        │
        ▼
PowerShell Started
        │
        ▼
Downloaded Malware
        │
        ▼
Registry Modified
        │
        ▼
Connected to C2 Server
```

One event alone may look normal.

When all events are connected together, they clearly indicate an attack.

---

# Why is Telemetry Useful for Analysts?

Telemetry helps analysts:

* See the complete attack chain.
* Understand what happened.
* Identify the root cause (main reason) of the incident.
* Build an attack timeline (sequence of events during the attack).
* Make accurate True Positive or False Positive decisions.

------------------------

# EDR Detection & Response 


# EDR Detection

After collecting **Telemetry (endpoint activity data)**, the EDR analyzes it using advanced detection techniques.

These techniques help detect attacks that traditional antivirus may miss.

---

# 1. Behavioral Detection

## What is Behavioral Detection?

Instead of only checking for **known malware signatures (digital fingerprints)**, EDR monitors the **behavior** of programs.

If a program behaves suspiciously, EDR generates an alert.

---

### Example

Normally,

```text
explorer.exe
      │
      ▼
winword.exe
```

But if:

```text
winword.exe
      │
      ▼
powershell.exe
```

This is suspicious because **Microsoft Word normally should not start PowerShell**.

EDR flags this unusual **Parent-Child Process Relationship (which process started another process).**

---

### Why is it Important?

It detects:

* Fileless malware
* Macro attacks
* Living-off-the-Land (LotL) attacks (attacks using legitimate system tools like PowerShell or CMD)
* Unknown malware

---

# 2. Anomaly Detection

## What is Anomaly Detection?

**Anomaly Detection** identifies activities that are different from the endpoint's normal behavior.

EDR first learns what is **normal**.

Then it detects anything unusual.

---

### Example

A computer has never modified the Windows startup registry.

Suddenly:

```text
powershell.exe

↓

Modifies Auto-Start Registry Key
```

This unusual activity is flagged.

---

### Why is it Important?

Detects:

* Insider threats
* New malware
* Suspicious administrator activity
* Unusual user behavior

> **Note:** Sometimes anomaly detection may generate **False Positives (normal activity incorrectly identified as malicious).**

---

# 3. IOC Matching

## What is IOC Matching?

**IOC (Indicator of Compromise - evidence that a system may be compromised)**

EDR compares collected telemetry with **Threat Intelligence (database of known malicious IPs, domains, file hashes, etc.)**.

If a match is found,

EDR immediately generates an alert.

---

### Common IOCs

* Malicious IP Address
* Malicious Domain
* File Hash
* Registry Key
* File Name

---

### Example

User downloads:

```text
invoice.exe
```

The file's **Hash (unique digital fingerprint of a file)** matches a known ransomware sample.

EDR immediately flags the file.

---

# 4. MITRE ATT&CK Mapping

## What is MITRE ATT&CK?

**MITRE ATT&CK** is a framework that maps attacker behaviors into:

* **Tactics (attacker's goal)**
* **Techniques (how the attacker achieves that goal)**

EDR automatically maps detections to MITRE.

---

### Example

Malware creates a Scheduled Task.

EDR maps it as:

```text
Tactic:

Persistence

Technique:

Scheduled Task/Job
```

---

### Why is it Useful?

It helps analysts understand:

* What stage of the attack they are seeing.
* What the attacker is trying to achieve.
* What might happen next.

---

# 5. Machine Learning (ML)

## What is Machine Learning?

**Machine Learning (AI that learns patterns from data)**

EDR trains on millions of:

* Normal activities
* Malicious activities

Then predicts whether new behavior is malicious.

---

### Example

Individually,

These activities look normal:

* Word opens.
* PowerShell starts.
* Network connection occurs.

Together,

Machine Learning recognizes the complete attack chain.

↓

Alert generated.

---

### Why is ML Important?

Helps detect:

* Zero-Day attacks (previously unknown vulnerabilities)
* Fileless malware
* Multi-stage attacks
* Advanced Persistent Threats (APTs)

---

# Detection Summary

| Detection Technique      | Detects                        |
| ------------------------ | ------------------------------ |
| **Behavioral Detection** | Suspicious behavior            |
| **Anomaly Detection**    | Unusual activities             |
| **IOC Matching**         | Known Indicators of Compromise |
| **MITRE Mapping**        | Attack stage and technique     |
| **Machine Learning**     | Complex attack patterns        |

---

# EDR Response

After detecting a threat, EDR allows analysts to respond immediately.

Response can be:

* Automatic
* Manual

---

# 1. Isolate Host

## What is Host Isolation?

Disconnect the infected endpoint from the network.

The computer still works,

but cannot communicate with other systems.

---

### Why?

Stops:

* Malware spreading
* Lateral Movement (attacker moving to other systems)
* Data theft

---

### Example

Laptop infected with ransomware.

↓

Click:

```text
Isolate Host
```

The laptop is removed from the network.

---

# 2. Terminate Process

## What is Process Termination?

Stop a running malicious process.

---

### Example

```text
malware.exe
```

↓

Terminate

The malware immediately stops running.

---

### Why?

Useful when:

* Only one process is malicious.
* Host isolation is unnecessary.

> **Be Careful:** Terminating a legitimate process may interrupt normal system operations.

---

# 3. Quarantine

## What is Quarantine?

Move a malicious file to a secure location where it cannot execute.

---

### Example

```text
invoice.exe

↓

Quarantine
```

The file becomes harmless.

Later,

the analyst can:

* Restore it (if safe).
* Permanently delete it.

---

# 4. Remote Access

## What is Remote Access?

EDR allows analysts to remotely connect to the endpoint's shell (command-line interface).

---

### Why?

Analysts can:

* Run commands.
* Execute scripts.
* Collect logs.
* Investigate deeper.
* Fix issues without visiting the device.

---

### Example

Using CrowdStrike RTR (Real Time Response):

```text
dir

ipconfig

tasklist
```

All commands can be executed remotely.

---

# 5. Artefact Collection

## What are Artefacts?

**Artefacts (digital evidence collected during an investigation)**

Used for:

* Digital Forensics
* Incident Investigation
* Legal Evidence

---

### Common Artefacts

### Memory Dump

**Memory Dump (copy of the computer's RAM)**

Useful for finding:

* Running malware
* Encryption keys
* Active processes

---

### Event Logs

Windows logs showing:

* Logins
* Errors
* Program executions

---

### Folder Contents

Copy important folders for investigation.

Example:

```text
Downloads

Desktop

Documents
```

---

### Registry Hives

**Registry Hives (main sections of the Windows Registry)**

Useful for finding:

* Startup programs
* User activity
* Malware persistence

---

# Response Summary

| Response Action         | Purpose                                        |
| ----------------------- | ---------------------------------------------- |
| **Isolate Host**        | Disconnect infected endpoint from the network  |
| **Terminate Process**   | Stop a malicious running process               |
| **Quarantine**          | Prevent a malicious file from executing        |
| **Remote Access**       | Investigate and respond using a remote shell   |
| **Artefact Collection** | Collect digital evidence for forensic analysis |

--------------------

