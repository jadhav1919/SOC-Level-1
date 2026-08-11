# Traditional SOC

## What is a Traditional SOC?

A **SOC (Security Operations Center)** is a central team that monitors and protects an organization's systems from cyber threats.

A SOC mainly uses:

* **People** → SOC analysts, engineers, incident responders
* **Processes** → Investigation, escalation, incident response
* **Technology** → SIEM, EDR, firewall, IAM, threat intelligence

---

# Main SOC Capabilities

## 1. Monitoring and Detection

The SOC continuously monitors systems and looks for **suspicious activity**.

Usually, the **SIEM** is the main tool used for monitoring.

### Example:

```text
Many failed login attempts
        ↓
SIEM detects activity
        ↓
SOC receives alert
        ↓
Analyst investigates
```

Other examples:

* Login from an unusual location
* Multiple failed logins
* Suspicious process execution
* Malware detection

### Simple meaning:

**Monitoring & Detection = Find threats**

---

# 2. Recovery and Remediation

When a threat is detected, the SOC takes action to **stop or reduce the damage**.

**Remediation (actions taken to fix or contain a security problem).**

Examples:

* Isolate an infected computer using **EDR**
* Block a malicious IP using a **firewall**
* Disable a compromised account using **IAM**
* Remove malware
* Stop a malicious process

### Simple meaning:

**Recovery & Remediation = Stop and fix the threat**

---

# 3. Threat Intelligence

**Threat Intelligence (information about known or emerging cyber threats)** helps SOC analysts understand whether something is malicious.

Threat intelligence can provide:

* Malicious IP addresses
* Malicious domains
* Malware hashes
* Threat actor information
* Attack techniques

### Example:

```text
Threat Intelligence
        ↓
Malicious IP identified
        ↓
SOC blocks the IP
```

### Simple meaning:

**Threat Intelligence = Know about threats**

---

# 4. Communication

SOC analysts don't work alone.

They communicate with:

* IT teams
* Management
* Security engineers
* HR
* Other departments

### Example:

A SOC detects a suspicious software installation.

The SOC may create a ticket for the **IT team** to verify whether the installation was legitimate.

### Simple meaning:

**Communication = Work with other teams**

---

# Traditional SOC Workflow

```text
Monitor
   ↓
Detect
   ↓
Investigate
   ↓
Respond
   ↓
Recover
   ↓
Communicate
```

The SOC uses multiple security tools during this process.

---

# Challenges Faced by Traditional SOCs

## 1. Alert Fatigue

**Alert Fatigue (analysts becoming overwhelmed because of too many alerts)** happens when security tools generate huge numbers of alerts.

Many alerts may be:

* False positives
* Low priority
* Duplicate alerts
* Normal activities

Example:

```text
1000 Alerts
    ↓
800 False Positives
150 Low Priority
50 Important
```

The analyst may become tired and potentially miss an important attack.

### Simple meaning:

**Too many alerts → Analyst gets overwhelmed → Real threats can be missed**

---

# 2. Too Many Disconnected Tools

Organizations use many security tools:

```text
Firewall
EDR
SIEM
IAM
Email Security
DLP
```

If these tools don't communicate with each other, analysts have to check each tool separately.

### Example:

```text
Firewall → Separate console
EDR      → Separate console
SIEM     → Separate console
IAM      → Separate console
```

This wastes time.

### Simple meaning:

**Disconnected tools = Too many consoles to check**

---

# 3. Manual Processes

Many SOC tasks are performed manually.

For example:

```text
Receive alert
   ↓
Open SIEM
   ↓
Check IP
   ↓
Check EDR
   ↓
Check threat intelligence
   ↓
Contact IT
   ↓
Block IP
```

Doing this manually for every alert takes a lot of time.

Some SOC procedures may also exist only as **tribal knowledge (knowledge known by experienced analysts but not properly documented)**.

### Simple meaning:

**Manual processes = Slow and repetitive work**

---

# 4. Talent Shortage

**Talent shortage (not having enough skilled cybersecurity professionals)** is another major SOC problem.

There are:

* More cyber threats
* More security alerts
* More complicated attacks
* Not enough experienced analysts

This increases the workload of existing analysts.

### Simple meaning:

**Not enough skilled people → More workload → Slower response**

--------------
# SOAR 

## What is SOAR?

**SOAR (Security Orchestration, Automation, and Response)** is a security platform that connects the different tools used by a SOC into one place.

Instead of an analyst switching between:

```text
SIEM → EDR → Firewall → Threat Intelligence → IAM → Ticketing
```

SOAR connects them together.

### Simple meaning:

> **SOAR = Connect tools + Automate tasks + Respond to threats**

---

# Why Do We Need SOAR?

Traditional SOCs have problems like:

* Too many alerts → **Alert fatigue**
* Too many separate tools → **Disconnected tools**
* Repetitive manual work → **Slow investigation**
* Not enough analysts → **Talent shortage**

SOAR helps reduce these problems.

---

# 3 Main Capabilities of SOAR

## 1. Orchestration

**Orchestration (connecting and coordinating different security tools)** means SOAR allows multiple tools to work together.

### Without SOAR:

An analyst investigating a VPN brute-force alert may manually check:

```text
SIEM
 ↓
Threat Intelligence
 ↓
IAM
 ↓
Firewall
 ↓
Ticketing System
```

The analyst has to switch between different tools.

### With SOAR:

```text
             ┌── SIEM
             ├── Threat Intelligence
SOAR ────────┼── IAM
             ├── Firewall
             └── Ticketing
```

Everything is connected through SOAR.

---

# Playbooks

A **Playbook (predefined step-by-step workflow for handling an alert)** tells SOAR what actions to perform.

### Example: VPN Brute Force Playbook

```text
VPN Brute Force Alert
        ↓
Check user's previous logins
        ↓
Check IP reputation
        ↓
Check for successful login
        ↓
Decide next action
```

The playbook can have different paths depending on the results.

### Example:

If:

```text
IP = Safe
Failed Attempts = Low
```

→ Stop investigation / close alert.

But if:

```text
IP = Malicious
Successful Login = Yes
```

→ Continue to containment.

### Simple meaning:

**Orchestration = Connect tools and follow a playbook**

---

# 2. Automation

**Automation (letting the system perform tasks automatically without manual clicks)** takes orchestration one step further.

Instead of the analyst manually performing every step, SOAR executes the playbook automatically.

### Example

SOAR receives:

```text
VPN Brute Force Alert
```

Then automatically:

```text
1. Checks user's login history
        ↓
2. Checks IP reputation
        ↓
3. Checks successful logins
        ↓
4. Detects malicious IP
        ↓
5. Disables compromised user
        ↓
6. Creates an incident ticket
```

The analyst doesn't have to perform every action manually.

### Simple meaning:

**Automation = SOAR does repetitive tasks automatically**

---

# 3. Response

**Response (actions taken to contain or stop a threat)** allows SOAR to take action through connected security tools.

### Example:

If a malicious IP is detected, SOAR can:

```text
Malicious IP
     ↓
SOAR
 ┌───┼────┬─────────┐
 ↓   ↓    ↓         ↓
Block Disable Create Notify
IP    User  Ticket  SOC
```

For example:

* Block IP on firewall.
* Disable compromised account through IAM.
* Create a ticket.
* Notify the SOC team.

### Simple meaning:

**Response = Take action against the threat**

---

# Orchestration vs Automation vs Response

| Term              | Simple Meaning                 |
| ----------------- | ------------------------------ |
| **Orchestration** | Connect and coordinate tools   |
| **Automation**    | Perform tasks automatically    |
| **Response**      | Take action to stop the threat |

### Easy memory:

> **Orchestration = Connect**
> **Automation = Do automatically**
> **Response = Act**

---

# Complete SOAR Example

Imagine the SOC receives a **VPN brute-force alert**.

### Traditional SOC

```text
Alert
 ↓
L1 opens SIEM
 ↓
Checks IP
 ↓
Opens Threat Intelligence
 ↓
Checks IP reputation
 ↓
Opens IAM
 ↓
Disables user
 ↓
Opens ticket
 ↓
Documents everything
```

This takes time.

---

### With SOAR

```text
Alert
 ↓
SOAR
 ↓
Runs Playbook
 ↓
Checks SIEM
 ↓
Checks Threat Intelligence
 ↓
Checks IAM
 ↓
Blocks IP / Disables User
 ↓
Creates Ticket
 ↓
Analyst reviews the result
```

Much of the repetitive work is automated.

---

# Does SOAR Replace SOC Analysts?

**No.**

SOAR helps analysts; it does not completely replace them.

### Why?

Some situations require:

* Human judgment
* Understanding business context
* Complex investigation
* Deciding whether activity is actually malicious
* Creating and improving playbooks

For example, SOAR may detect:

```text
Suspicious login
```

But an analyst may need to decide:

> "Is this actually an attack, or is the employee travelling to another country?"

SOAR can automate the investigation steps, but **human judgment is still important**.

---

# Role of SOC Analyst with SOAR

Instead of spending most of the time on repetitive tasks, analysts can focus on:

```text
Complex Investigations
        ↓
Threat Hunting
        ↓
Incident Response
        ↓
Creating Playbooks
        ↓
Improving Detection
```

---

# Traditional SOC vs SOAR

| Traditional SOC         | SOC with SOAR         |
| ----------------------- | --------------------- |
| Manual investigation    | Automated workflows   |
| Many separate tools     | Connected tools       |
| Many manual clicks      | Automated actions     |
| Slower response         | Faster response       |
| More repetitive work    | Less repetitive work  |
| Higher analyst workload | Reduced workload      |
| More alert fatigue      | Reduced alert fatigue |

-------------------
# SOAR Playbooks 

## What is a Playbook?

A **Playbook (predefined step-by-step workflow)** tells the SOAR tool what to do when a specific type of security alert occurs.

Instead of an analyst deciding every step manually:

```text
Alert → Check → Investigate → Respond
```

SOAR follows the predefined workflow automatically.

A playbook usually contains **IF/ELSE decisions**:

```text
IF suspicious URL exists
    ↓
Check URL
ELSE
    ↓
Continue with another action
```

---

# 1. Phishing Playbook

A **Phishing Playbook** is used to investigate suspicious emails.

Phishing investigation can take a lot of time because analysts may need to check:

* URLs
* Attachments
* File hashes
* Threat intelligence
* Email details

SOAR can automate many of these checks.

## Basic Flow

```text
Suspicious Email Received
          ↓
Create Investigation Ticket
          ↓
Does email contain URL or attachment?
       /             \
     No               Yes
     ↓                 ↓
Notify Users       Check URL/Attachment
                       ↓
              ┌────────┴────────┐
              ↓                 ↓
           URL?             Attachment?
              ↓                 ↓
       VirusTotal Check   Calculate Hash
              ↓                 ↓
       Is URL malicious?  VirusTotal Check
              ↓                 ↓
           Yes/No        Is Hash malicious?
              └────────┬────────┘
                       ↓
                Manual Analysis
                       ↓
                Is it malicious?
                  /          \
                Yes           No
                 ↓             ↓
            Delete Email    Continue/Close
                 ↓
          Update Ticket
```

### Important idea

SOAR can automatically:

* Check URLs.
* Calculate file hashes.
* Check VirusTotal.
* Delete malicious emails.
* Update the investigation ticket.

But a **SOC analyst may still perform manual analysis** when the result is unclear.

### Simple meaning:

> **Phishing Playbook = Automatically investigate suspicious emails and remove malicious ones.**

---

# 2. CVE Patching Playbook

A **CVE (Common Vulnerabilities and Exposures)** is a publicly identified software vulnerability assigned a CVE number.

When a new CVE is released, the organization needs to determine:

> "Are our systems affected?"

If yes, the vulnerability should be patched.

---

## Basic Flow

```text
New Security Advisory
        ↓
Extract CVE Information
        ↓
Check if CVE is already addressed
        ↓
       Yes ─────────→ END
        │
        No
        ↓
Check if CVE applies to the organization
        ↓
       No ──────────→ Update Patch Management → END
        │
       Yes
        ↓
Create CVE Ticket
        ↓
SOC Analyst Reviews
        ↓
Identify Affected Assets
        ↓
Check Patch Availability
       /        \
     Yes         No
      ↓           ↓
Create Test    Update Path
Environment    Database
      ↓           ↓
Test Patch ←─────┘
      ↓
Apply Patch
      ↓
Monitor Performance
      ↓
Update SOC Ticket
      ↓
Verify Patch
      ↓
Are Assets Still Vulnerable?
       /          \
     Yes           No
      ↓             ↓
Create           Close CVE
Mitigation       Ticket
      ↓             ↓
Deploy           Update
Mitigation       Management
```

---

# What Does SOAR Automate?

In these playbooks, SOAR can perform many repetitive tasks.

### Phishing

```text
Check URL
Check Hash
Query VirusTotal
Delete Email
Update Ticket
```

### CVE

```text
Check CVE
Check Patch Status
Identify Assets
Create Tickets
Apply/Coordinate Patches
Update Records
```

---

# Where Does the SOC Analyst Come In?

Notice that the analyst is **still present** in both playbooks.

This is important.

SOAR handles:

> **Repetitive and predictable tasks**

SOC analysts handle:

> **Important decisions and verification**

For example:

### SOAR says:

```text
Hash = Unknown
```

The analyst may need to manually investigate the file.

Or:

### SOAR identifies:

```text
CVE affects 20 servers
```

The analyst may need to review the affected systems and decide how the patch should be deployed safely.

---

# Why Playbooks Are Important

Without a playbook:

```text
Alert
 ↓
Analyst thinks:
"What should I check?"
 ↓
Manually checks different tools
 ↓
Manually investigates
 ↓
Manually responds
```

With a playbook:

```text
Alert
 ↓
SOAR Playbook
 ↓
Automated Investigation
 ↓
Decision Point
 ↓
Analyst Verification
 ↓
Automated Response
```

This makes the SOC:

* Faster
* More consistent
* Less dependent on manual work
* Less affected by alert fatigue
* More efficient

---

# Key Difference

| Playbook                  | Purpose                                         |
| ------------------------- | ----------------------------------------------- |
| **Phishing Playbook**     | Investigate and respond to suspicious emails    |
| **CVE Patching Playbook** | Identify vulnerable systems and manage patching |

---------------------


