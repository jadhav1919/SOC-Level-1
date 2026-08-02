# SOC Metrics 
# What are SOC Metrics?

**SOC Metrics** are measurements used to evaluate how well a Security Operations Center (SOC) is performing.

They help answer questions like:

* Are analysts overloaded?
* Are detection rules effective?
* Are analysts missing attacks?
* Is the SOC detecting real threats?

---

# Main Goal of a SOC

The main goal of a SOC is to protect the **CIA Triad**.

* **Confidentiality** → Prevent unauthorized access to data.
* **Integrity** → Prevent unauthorized modification of data.
* **Availability** → Ensure systems and services remain accessible.

SOC analysts achieve this by:

* Monitoring alerts
* Investigating alerts
* Detecting threats
* Escalating real attacks

# 1. Alerts Count (AC)

## Definition

**Alerts Count** is the total number of alerts received by the SOC.

### Formula

```text
Alerts Count (AC) = Total Alerts Received
```

### What does it measure?

The workload of SOC analysts.

### Example

A SOC receives:

* Monday → 15 alerts
* Tuesday → 18 alerts
* Wednesday → 20 alerts

**Alerts Count = 53**

### Good Value

Approximately **5–30 alerts per day per L1 analyst**.

### Too High?

Example:

80–100 alerts/day

Problems:

* Analysts become overwhelmed.
* Important alerts may be missed.
* Investigation quality decreases.

### Too Low?

Example:

0–2 alerts/day

Problems:

* SIEM may not be collecting logs.
* Detection rules may be broken.
* Threats may go undetected.

# 2. False Positive Rate (FPR)

## Definition

The percentage of alerts that are **not real threats**.

### Formula

```text
False Positive Rate (FPR)

=
False Positives
--------------------
Total Alerts
```

### Example

Total Alerts = 100

False Positives = 80

```text
FPR = 80 / 100 = 80%
```

### What does it measure?

The amount of **noise** in the SOC.

### High False Positive Rate

Problems:

* Analysts waste time.
* Alert fatigue occurs.
* Real attacks may be ignored.

### Example

100 alerts

* 95 False Positives
* 5 True Positives

Analysts spend most of their time investigating harmless activity.


### Solution

Improve detection rules by:

* Fine-tuning SIEM rules.
* Updating detection logic.
* Reducing unnecessary alerts.

This process is called **False Positive Remediation**.


# 3. Alert Escalation Rate (AER)

## Definition

The percentage of alerts that L1 analysts escalate to L2.

### Formula

```text
Alert Escalation Rate (AER)

=
Escalated Alerts
------------------------
Total Alerts
```

### Example

Total Alerts = 50

Escalated Alerts = 10

```text
AER = 10 / 50 = 20%
```

---

### What does it measure?

How often L1 analysts require help from L2.

---

### High Escalation Rate

Example:

80%

Possible reasons:

* L1 analysts are inexperienced.
* Analysts lack confidence.
* Too many complex alerts.

---

### Low Escalation Rate

Can be good **only if investigations are accurate**.

If too low because analysts close alerts without understanding them, it becomes dangerous.

---

### Good Value

Usually:

Less than **20%**

Acceptable:

Less than **50%**

---

# 4. Threat Detection Rate (TDR)

## Definition

Measures how many real attacks the SOC successfully detects.

### Formula

```text
Threat Detection Rate (TDR)

=
Detected Threats
--------------------
Total Threats
```

---

### Example

During one month:

Total attacks = 6

Detected = 4

Missed = 2

```text
TDR = 4 / 6 = 67%
```

---

### What does it measure?

The effectiveness of the SOC.

---

### Good Value

**100%**

Every real attack should ideally be detected.

---

### Why is a Low TDR Dangerous?

Missed threats can lead to:

* Ransomware
* Data theft
* Credential theft
* Financial loss
* Business disruption

---

# Summary Table

| Metric                          | Formula                          | Measures          | Good Value                        |
| ------------------------------- | -------------------------------- | ----------------- | --------------------------------- |
| **Alerts Count (AC)**           | Total Alerts Received            | Analyst workload  | 5–30 alerts/day per L1            |
| **False Positive Rate (FPR)**   | False Positives ÷ Total Alerts   | Alert noise       | As low as possible                |
| **Alert Escalation Rate (AER)** | Escalated Alerts ÷ Total Alerts  | L1 experience     | <20% (Ideally), <50% (Acceptable) |
| **Threat Detection Rate (TDR)** | Detected Threats ÷ Total Threats | SOC effectiveness | 100%                              |

--------------
# SOC SLA Metrics 

# What is an SLA?

**SLA (Service Level Agreement)** is a document that defines the expected performance and response time of the SOC.

It is an agreement between:

* **Internal SOC** ↔ Company Management
* **MSSP** ↔ Customer

The SLA ensures the SOC responds to security incidents within an agreed time.


# Why is SLA Important?

An alert alone **does not stop an attack**.

The SOC must:

1. Detect the attack quickly.
2. Start investigating immediately.
3. Respond before the attacker causes damage.

The faster these steps happen, the less damage the attacker can do.

# Attack Timeline

```text
Attack Starts
      │
      ▼
SOC Tool Detects Attack
      │
<------ MTTD ------>
      │
      ▼
L1 Analyst Opens Alert
      │
<------ MTTA ------>
      │
      ▼
SOC Responds
(Isolate Host / Reset Password)
      │
<------ MTTR ------>
      │
      ▼
Attack Stopped
```

# 1. Mean Time to Detect (MTTD)

## Definition

**MTTD** is the average time taken by SOC security tools to detect an attack.

### Formula

```text
MTTD = Attack Started → Alert Generated
```

### Example

```text
10:00 AM → Attacker starts attack

10:04 AM → SIEM generates alert
```

```text
MTTD = 4 Minutes
```

### What does it measure?

How quickly the security tools detect attacks.

### Good SLA

Usually around:

**5 Minutes**


### Lower is Better

Smaller MTTD = Faster detection

# 2. Mean Time to Acknowledge (MTTA)

## Definition

**MTTA** is the average time taken by an L1 analyst to acknowledge and start investigating a new alert.

### Formula

```text
MTTA = Alert Generated → L1 Starts Investigation
```

### Example

```text
10:04 AM → Alert Created

10:08 AM → L1 opens alert
```

```text
MTTA = 4 Minutes
```

### What does it measure?

How quickly analysts respond to new alerts.


### Good SLA

Usually around:

**10 Minutes**

### Lower is Better

The faster an analyst starts investigating, the better.


# 3. Mean Time to Respond (MTTR)

## Definition

**MTTR** is the average time taken to stop or contain the attack after it has been detected.

### Formula

```text
MTTR = Alert Generated → Threat Contained
```

### Example

```text
10:04 AM → Alert Generated

10:45 AM → Infected device isolated
```

```text
MTTR = 41 Minutes
```

---

### What does it measure?

How quickly the SOC responds and stops the attack.

### Good SLA

Usually around:

**60 Minutes**

### Examples of Response

* Isolate infected computer
* Reset compromised password
* Disable user account
* Block malicious IP
* Remove malware
* Quarantine phishing email

# SLA Reference Table

| Metric                    | Measures                           | Typical SLA                         |
| ------------------------- | ---------------------------------- | ----------------------------------- |
| **SOC Team Availability** | When the SOC operates              | 24/7 (or 8×5 in some organizations) |
| **MTTD**                  | Time to detect an attack           | **5 Minutes**                       |
| **MTTA**                  | Time for L1 to start investigating | **10 Minutes**                      |
| **MTTR**                  | Time to stop or contain the attack | **60 Minutes**                      |


# Real Example

Suppose:

```text
10:00 AM

Attacker starts phishing attack
```

↓

```text
10:03 AM

SIEM detects phishing
```

↓

```text
10:07 AM

L1 analyst opens alert
```

↓

```text
10:35 AM

Email removed

User account secured
```

### Calculate

```text
MTTD

10:03 - 10:00

= 3 Minutes
```

---

```text
MTTA

10:07 - 10:03

= 4 Minutes
```

---

```text
MTTR

10:35 - 10:03

= 32 Minutes
```


All SLA targets are successfully met.


# Easy Way to Remember

| Metric   | Remember As                                                        |
| -------- | ------------------------------------------------------------------ |
| **MTTD** | **Detect** → When did the SOC tool detect the attack?              |
| **MTTA** | **Acknowledge** → When did the analyst start working on the alert? |
| **MTTR** | **Respond** → When was the attack stopped or contained?            |

-----------
# Improving SOC Metrics

# Why Do SOC Metrics Matter?

SOC metrics are used to:

* Measure SOC performance.
* Reduce successful cyber attacks.
* Identify weaknesses in the SOC.
* Improve security operations.
* Evaluate analyst performance.

As an **L1 Analyst**, good performance can lead to:

* Promotion to **L2 Analyst**
* Better performance reviews
* Increased responsibilities
* Career growth

# How to Improve SOC Metrics

## 1. False Positive Rate (FPR)

### Problem

**False Positive Rate > 80%**

The SOC is receiving too many unnecessary alerts.

Analysts waste time investigating alerts that are not real threats.


### How to Improve

**1. Tune Detection Rules**

Exclude trusted or normal activities.

Examples:

* Windows Updates
* Antivirus updates
* Scheduled backups
* Approved admin scripts

This reduces unnecessary alerts.


**2. Automate Repetitive Alerts**

Use:

* SOAR
* Automation scripts

Examples:

* Automatically close known false positives.
* Automatically enrich alerts with user or asset information.

Result:

* Less manual work.
* Faster investigations.

# 2. Mean Time to Detect (MTTD)

### Problem

**MTTD > 30 Minutes**

The SOC detects attacks too slowly.

Attackers have more time to spread and cause damage.


### How to Improve

**1. Improve Detection Rules**

Ask SOC Engineers to:

* Run detection rules more frequently.
* Optimize slow queries.
* Improve SIEM performance.

**2. Collect Logs in Real Time**

Ensure logs reach the SIEM immediately.

Example:

 Logs arrive after 10 minutes.

↓

SOC detects attacks 10 minutes late.

Better:

 Logs arrive instantly.

↓

Faster detection.


# 3. Mean Time to Acknowledge (MTTA)

### Problem

**MTTA > 30 Minutes**

L1 analysts take too long to begin investigating alerts.


### How to Improve

**1. Enable Real-Time Notifications**

Examples:

* SIEM pop-up alerts.
* Email notifications.
* Microsoft Teams or Slack notifications.
* Mobile alerts.

Analysts are notified immediately when new alerts arrive.


**2. Balance the Workload**

Distribute alerts evenly among analysts.

Example:

❌ Analyst A → 40 alerts

❌ Analyst B → 5 alerts

Better:

✔ Analyst A → 20 alerts

✔ Analyst B → 20 alerts

This reduces waiting time.

---

# 4. Mean Time to Respond (MTTR)

### Problem

**MTTR > 4 Hours**

The SOC is too slow to contain the attack.

Attackers have more time to:

* Spread malware
* Steal data
* Move laterally
* Deploy ransomware

---

### How to Improve

**1. Escalate Quickly**

As an L1 analyst:

* Complete your investigation.
* Write a clear report.
* Escalate immediately if needed.

Do not delay major incidents.

---

**2. Create Incident Response Playbooks**

Every common attack should have a documented response.

Examples:

* Malware
* Phishing
* Ransomware
* Data Exfiltration
* Brute Force Attack

Playbooks help analysts respond faster and consistently.

-------------------------
