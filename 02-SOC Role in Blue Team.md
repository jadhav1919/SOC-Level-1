
# Security Leadership Structure

```text
CEO
 │
 ▼
Chief Information Security Officer (CISO)
 │
 ├──────────────┬──────────────┬──────────────┐
 ▼              ▼              ▼
Red Team     Blue Team      GRC Team
 │
 ▼
SOC / CIRT / Other Security Teams
```

## CEO (Chief Executive Officer)

The CEO is responsible for the entire company.

### Responsibilities

* Business strategy
* Company growth
* Finance
* Management

The CEO usually **does not manage technical security tasks**.


## CISO (Chief Information Security Officer)

The **CISO** leads the organisation's cyber security programme.

### Responsibilities

* Create security strategy
* Protect company assets
* Manage security teams
* Report security risks to executives
* Ensure business security objectives are met

Think of the CISO as the **head of cyber security**.


# Security Departments

Large organisations divide security work into specialised teams.


# 1. Red Team

### Purpose

Acts like an attacker to find weaknesses before real hackers do.

### Responsibilities

* Penetration Testing
* Ethical Hacking
* Vulnerability Assessment
* Social Engineering
* Security Testing

### Goal

Find vulnerabilities before attackers exploit them.


# 2. Blue Team

### Purpose

Defend the organisation against cyber attacks.

### Responsibilities

* Monitor systems
* Detect attacks
* Investigate alerts
* Respond to incidents
* Improve security

The Blue Team works continuously to protect the organisation.

# 3. GRC Team

**GRC = Governance, Risk, and Compliance**

### Responsibilities

* Create security policies
* Assess risks
* Ensure compliance with regulations
* Perform audits

### Examples of standards

* PCI DSS
* ISO 27001
* GDPR
* HIPAA

# Blue Team Departments

A Blue Team is often divided into several specialised teams.


# Security Operations Center (SOC)

The **SOC** is the central hub of cyber defence.

It monitors systems **24/7** and responds to security alerts.


## SOC Structure

### L1 Analyst (Junior Analyst)

Responsibilities

* Monitor alerts
* Investigate basic incidents
* Escalate complex cases
* Document findings

This is usually the **entry-level role**.


### L2 Analyst

Responsibilities

* Investigate advanced attacks
* Perform deeper analysis
* Help L1 analysts
* Handle difficult incidents


### SOC Engineer

Responsibilities

* Configure SIEM
* Configure EDR
* Create detection rules
* Improve alert quality
* Reduce false positives


### SOC Manager

Responsibilities

* Manage the SOC team
* Report to management
* Improve SOC operations
* Coordinate major incidents

# Cyber Incident Response Team (CIRT)

Also called:

* CSIRT
* CERT

The CIRT handles **major cyber incidents** that require advanced investigation.

Think of them as the **firefighters** of cyber security.

## Responsibilities

* Incident Response
* Digital Forensics
* Malware Analysis
* Threat Hunting
* Threat Intelligence
* System Recovery


## When is CIRT involved?

Examples:

* Ransomware attack
* Large data breach
* Advanced Persistent Threat (APT)
* Nation-state attack
* Company-wide malware outbreak

# Common CIRT Roles

### Digital Forensics Analyst

Investigates evidence from:

* Hard disks
* Memory (RAM)
* Mobile devices
* Log files

Goal:

Determine **what happened**, **how it happened**, and **who was responsible**.

### Threat Intelligence Analyst

Studies cyber threat actors.

Responsibilities

* Track hacker groups
* Monitor emerging threats
* Analyse Indicators of Compromise (IOCs)
* Share intelligence with SOC

### Threat Hunter

Looks for hidden attackers that automated tools may miss.

Instead of waiting for alerts, they **proactively search** for suspicious activity.


### Malware Analyst

Studies malicious software.

Responsibilities

* Reverse engineer malware
* Understand behaviour
* Create detection signatures
* Recommend defences

# Other Specialised Security Roles

## AppSec Engineer

Focuses on making software secure.

Responsibilities

* Secure coding
* Code reviews
* Vulnerability testing
* Secure Software Development Lifecycle (SSDLC)


## AI Security Researcher

Researches:

* AI attacks
* AI defence techniques
* Secure AI systems

# SOC Career Path

```text
SOC L1 Analyst
      │
      ▼
SOC L2 Analyst
      │
      ├───────────────┐
      ▼               ▼
SOC Engineer      Incident Responder
      │               │
      ▼               ▼
Security Architect  Threat Hunter
      │
      ▼
SOC Manager
      │
      ▼
CISO
```

> Career paths vary by organisation. Some professionals specialise (e.g., AppSec or Threat Intelligence) rather than moving into management.


# How to Become a SOC Analyst

### 1. Learn Core Skills

* Networking
* Windows
* Linux
* Security Fundamentals
* SIEM
* EDR
* Incident Response


### 2. Practice

Use platforms like:

* TryHackMe
* Hack The Box
* CTF competitions


### 3. Stay Updated

Follow:

* Cyber security news
* New vulnerabilities
* Malware trends
* Threat intelligence reports


### 4. Prepare for Interviews

Learn:

* SOC processes
* Incident handling
* Log analysis
* Common attack techniques


# Internal SOC vs MSSP

**MSSP = Managed Security Services Provider**

An MSSP provides outsourced security monitoring for multiple organisations.

| Feature           | Internal SOC                      | MSSP                                           |
| ----------------- | --------------------------------- | ---------------------------------------------- |
| Who you protect   | One organisation                  | Many clients                                   |
| Work pace         | Usually steadier                  | Fast-paced and high-pressure                   |
| Security tools    | Fewer tools, deep expertise       | Many different tools and environments          |
| Incident exposure | Fewer major incidents             | Frequent exposure to a wide variety of attacks |
| Learning          | Deep knowledge of one environment | Broad experience across many industries        |


# Advantages

## Internal SOC

 Better understanding of one company's environment

 Stable work

 Deeper knowledge of internal systems


## MSSP

 Faster learning

Exposure to many attack types

 Experience with many security tools

 Strong career growth opportunities



# Best Practices for SOC Analysts

### Learn from every alert

Even false positives help you understand normal behaviour and improve your investigation skills.


### Think like an attacker

Ask yourself:

* How did the attacker get in?
* What would they do next?
* How can we stop them?


### Verify everything

Never assume an alert is correct.

Always collect evidence before making a decision.

### Participate in incidents

Real-world incidents provide valuable practical experience.


