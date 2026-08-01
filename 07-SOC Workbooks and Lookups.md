# Identity Inventory & Asset Inventory

# Scenario

Suppose you receive the following alert:

> **G.Baker** logged into **HQ-FINFS-02**, downloaded **Financial Report US 2024.xlsx**, and shared it with **R.Lund**.

Before deciding whether this is malicious or normal, you need more information.

Questions you should ask:

* Who is **G.Baker**?
* What is G.Baker's job role?
* Is G.Baker allowed to access the server?
* What is **HQ-FINFS-02**?
* Who owns this server?
* Why was the file shared with **R.Lund**?
* Is this normal business activity?

This is where **Identity Inventory** and **Asset Inventory** help.

---

# Identity Inventory

## Definition

**Identity Inventory** is a database of all users and service accounts in an organization.

It helps SOC analysts understand **who** is performing an action.

---

## Information Stored

An Identity Inventory may contain:

* Full Name
* Username
* Email Address
* Job Role
* Department
* Manager
* Office Location
* Working Hours
* User Privileges
* Groups
* Access Permissions

---

## Example

| Full Name     | Username | Role                    | Location | Access           |
| ------------- | -------- | ----------------------- | -------- | ---------------- |
| Gregory Baker | G.Baker  | Chief Financial Officer | UK       | VPN, HQ, Finance |
| Raymond Lund  | R.Lund   | Financial Adviser       | Texas    | VPN, Finance     |
| Kate Danner   | K.Danner | CTO                     | UK       | VPN, HQ, AWS     |

---

## Why is Identity Inventory Important?

It helps answer questions like:

* Who is the user?
* What is their role?
* Should they access this server?
* Is the login location normal?
* Does the user normally work at this time?

### Example

Alert:

> G.Baker accessed a finance server.

Identity Inventory shows:

* Role: **Chief Financial Officer**
* Department: **Finance**

This makes the activity more likely to be legitimate.

---

# Service Accounts

Not every account belongs to a person.

Some accounts belong to applications or services.

Example:

```
svc-veeam-06
svc-nginx-pp
```

These accounts automatically perform tasks like:

* Backups
* Running web servers
* Scheduled jobs

SOC analysts should know the difference between **user accounts** and **service accounts**.

---

# Sources of Identity Information

| Source                | Examples               | Purpose                               |
| --------------------- | ---------------------- | ------------------------------------- |
| Active Directory (AD) | On-prem AD, Entra ID   | Stores users, groups, and permissions |
| SSO Providers         | Okta, Google Workspace | Cloud identity management             |
| HR Systems            | BambooHR, SAP, HiBob   | Employee information                  |
| Custom Database       | Excel, CSV             | Internal employee records             |

---

# Asset Inventory

## Definition

**Asset Inventory** is a database of all devices and systems in an organization.

It helps SOC analysts understand **what system** is involved in an alert.

---

## Information Stored

An Asset Inventory may contain:

* Hostname
* IP Address
* Operating System
* Owner
* Device Type
* Physical Location
* Purpose
* Department

---

## Example

| Hostname    | OS                  | Owner      | Purpose              |
| ----------- | ------------------- | ---------- | -------------------- |
| HQ-FINFS-02 | Windows Server 2022 | Central IT | Finance File Server  |
| HQ-ADDC-01  | Windows Server 2019 | Central IT | Domain Controller    |
| PC-891D     | Windows 11          | Accounting | Employee Workstation |

---

## Why is Asset Inventory Important?

It helps answer questions like:

* What is this server?
* Who owns it?
* Where is it located?
* What is its purpose?
* Is it a critical system?

### Example

Alert:

> Malware detected on **HQ-ADDC-01**.

Asset Inventory shows:

* Purpose: **Primary Domain Controller**

This is a **critical server**, so the alert becomes very high priority.

---

# Sources of Asset Information

| Source           | Examples               | Purpose                                  |
| ---------------- | ---------------------- | ---------------------------------------- |
| Active Directory | AD, Entra ID           | Computer inventory                       |
| SIEM / EDR       | Elastic, CrowdStrike   | Device information collected from agents |
| MDM              | Microsoft Intune, Jamf | Laptop and mobile device management      |
| Custom Inventory | Excel, CSV             | Internal asset database                  |

---

# Identity Inventory vs Asset Inventory

| Identity Inventory                     | Asset Inventory                              |
| -------------------------------------- | -------------------------------------------- |
| Stores information about users         | Stores information about devices and servers |
| Answers **Who?**                       | Answers **What System?**                     |
| Includes role, department, permissions | Includes hostname, OS, owner, purpose        |
| Used to verify user activity           | Used to understand system importance         |

---

# Investigation Example

### Alert

```
User: G.Baker

Server: HQ-FINFS-02

Action:
Downloaded Financial Report US 2024.xlsx
Shared with R.Lund
```

---

### Step 1 — Check Identity Inventory

**G.Baker**

* Role: Chief Financial Officer
* Department: Finance
* Access: Finance Servers

 Access is expected.

---

**R.Lund**

* Role: US Financial Adviser
* Department: Finance

 Receiving financial reports may be expected.

---

### Step 2 — Check Asset Inventory

**HQ-FINFS-02**

* Windows Server 2022
* UK Datacenter
* Purpose: Finance File Server
* Owner: Central IT

This confirms the server stores financial documents.

---

### Step 3 — Conclusion

Since:

* G.Baker is the CFO.
* HQ-FINFS-02 is the Finance File Server.
* R.Lund is a Financial Adviser.

The activity **may be legitimate**, but you should still verify:

* Was the file sharing expected?
* Was it during normal working hours?
* Was the destination approved?

------------------

# Network Diagrams 

# Scenario

Suppose you receive these firewall alerts:

```
08:00  103.61.240.174 → Connected to Firewall on TCP/10443

08:23  103.61.240.174 → Translated to Internal IP 10.10.0.53

08:25  10.10.0.53 → Scanning 172.16.15.0/24

08:32  10.10.0.53 → Scanning 172.16.23.0/24
```

At first glance, these are just IP addresses. As a SOC analyst, you need to answer questions like:

* What service is running on **Port 10443**?
* What is **10.10.0.53**?
* Which subnet does it belong to?
* Why is it scanning other networks?

To answer these questions, we use a **Network Diagram**.

---

# What is a Network Diagram?

A **Network Diagram** is a visual map of an organization's network.

It shows:

* Internet connection
* Firewalls
* VPN
* Servers
* Office network
* Database network
* Subnets
* Network connections

It helps SOC analysts understand **how devices and networks are connected**.

---

# Example Network Diagram

```
                   Internet
                       │
                       ▼
                Corporate Firewall
                 │      │
        TCP 10443│      │HTTP/HTTPS
                 ▼      ▼
               VPN     Web Server
                 │
        -------------------------
        │           │
        ▼           ▼
 Database Subnet   Office Subnet
172.16.15.0/24   172.16.23.0/24
```

---

# Understanding the Attack

## Alert 1

```
08:00

103.61.240.174

↓

Connected to TCP Port 10443
```

### Question

What is Port **10443**?

### Answer

The Network Diagram shows that **Port 10443 is the VPN service**.

So the attacker is trying to access the company's VPN.

---

## Alert 2

```
08:23

103.61.240.174

↓

10.10.0.53
```

### What happened?

The external IP successfully authenticated to the VPN.

The VPN assigned the attacker an internal IP:

```
10.10.0.53
```

This IP belongs to the **VPN Subnet (10.10.0.0/16)**.

---

## Alert 3

```
08:25

10.10.0.53

↓

172.16.15.0/24
```

### What happened?

The attacker started scanning the **Database Subnet**.

Purpose:

* Find servers
* Find open ports
* Find vulnerable systems

The firewall likely blocked the scan because no open ports were found.

---

## Alert 4

```
08:32

10.10.0.53

↓

172.16.23.0/24
```

### What happened?

The attacker changed targets.

Now they are scanning the **Office Subnet** hoping to find accessible systems.

---

# Reconstructing the Attack

Using the Network Diagram, we can understand the complete attack.

### Step 1

Attacker connects from the Internet.

```
Internet

↓

103.61.240.174
```

---

### Step 2

Attempts VPN login.

```
Port 10443

↓

VPN Server
```

---

### Step 3

VPN login succeeds.

The attacker receives an internal IP.

```
10.10.0.53
```

---

### Step 4

Scans the Database Network.

```
172.16.15.0/24
```

No success.

---

### Step 5

Moves to another subnet.

```
172.16.23.0/24

(Office Network)
```

The attack is still ongoing.

---

# Complete Attack Flow

```
Internet Attacker
103.61.240.174
        │
        ▼
Corporate Firewall
        │
        ▼
VPN Login (TCP 10443)
        │
        ▼
Assigned VPN IP
10.10.0.53
        │
        ▼
Scans Database Network
172.16.15.0/24
        │
        ▼
Blocked
        │
        ▼
Scans Office Network
172.16.23.0/24
```

----------------------------

# SOC Workbooks (Playbooks / Runbooks)

# What is a SOC Workbook?

A **SOC Workbook** is a **step-by-step guide** that helps analysts investigate and respond to a specific type of security alert.

It ensures every analyst follows the same investigation process.

> **Other Names:**
>
> * Playbook
> * Runbook
> * Workflow

# Why Do SOC Teams Use Workbooks?

Without a workbook:

* Analysts may forget important investigation steps.
* Different analysts may investigate the same alert differently.
* Important evidence may be missed.

With a workbook:

* Every alert is investigated consistently.
* Mistakes are reduced.
* New (L1) analysts can investigate alerts more confidently.
* Investigations become faster and more accurate.

# Example

### Alert

```text
Alert Name:
Unusual Login Location

User:
john.smith

Login Location:
Russia

Normal Location:
United Kingdom
```

Instead of guessing whether the login is malicious, the analyst follows the **Workbook**.

# Main Phases of a Workbook

Every workbook is generally divided into **three phases**.

## 1. Enrichment

### Purpose

Collect additional information before making a decision.

### Check:

* User details (Identity Inventory)
* Host details (Asset Inventory)
* Threat Intelligence
* IP reputation
* Login location
* User role
* Working hours

### Example

User:

```text
John Smith
```

Identity Inventory shows:

* Finance Department
* Works in London
* Office hours: 9 AM – 6 PM

Threat Intelligence shows:

* Login IP is associated with a VPN service.

Now you have more context.

## 2. Investigation

### Purpose

Analyze the alert using all available evidence.

### Check:

* SIEM logs
* Previous logins
* Failed login attempts
* Related alerts
* User activity
* Network activity
* Process execution

### Example

You discover:

* User logged in from London at 9:00 AM.
* Five minutes later, another login appears from Russia.

Since it is impossible to log in from two countries within five minutes, the activity is suspicious.

## 3. Escalation

### Purpose

Decide the next action.

Possible actions:

* Close as False Positive.
* Escalate to L2.
* Contact the user.
* Start Incident Response.

### Example

The login is confirmed to be malicious.

Action:

* Escalate to L2.
* Contact the user.
* Reset credentials.
* Investigate further.

# Workbook Flow

```text
Alert Received
       │
       ▼
Enrichment
(User, Asset, Threat Intel)
       │
       ▼
Investigation
(SIEM Logs, Related Events)
       │
       ▼
Decision
       │
 ┌─────┴─────┐
 │           │
Safe      Suspicious
 │           │
Close     Escalate to L2
```

# Benefits of a Workbook

* Standardizes investigations.
* Ensures no important steps are missed.
* Helps junior analysts.
* Reduces investigation time.
* Improves investigation quality.
* Makes SOC operations consistent.

-----------------


