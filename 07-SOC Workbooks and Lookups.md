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

