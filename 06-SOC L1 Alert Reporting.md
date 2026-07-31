## Why Should L1 Analysts Write Alert Reports?

Writing an alert report is much more than simply marking an alert as **True Positive** or **False Positive**. A good report helps other team members understand the investigation and ensures nothing important is missed.

### 1. Provide Context for Escalation

When an alert is escalated to an **L2 Analyst**, they should not have to start the investigation from the beginning.

A well-written report:

* Explains what happened.
* Shows what was already investigated.
* Includes important evidence.
* Saves L2 analysts time.
* Speeds up incident response.

**Example:**
Instead of just writing **"True Positive"**, explain:

* What was detected?
* Which user or system was affected?
* What evidence was found?
* Why was it escalated?


### 2. Save Investigation Records

SIEM logs are usually stored for only **3 to 12 months**, but alert records are often kept much longer.

An alert report acts as permanent documentation of the investigation.

Benefits:

* Helps during future investigations.
* Useful for audits and compliance.
* Provides historical evidence if the same threat appears again.
* Makes it easy to understand past incidents.


### 3. Improve Investigation Skills

Writing reports forces analysts to organize their thoughts.

A common saying is:

> **"If you can't explain it simply, you don't understand it well enough."**

Benefits:

* Improves analytical thinking.
* Builds better investigation skills.
* Helps identify missing evidence.
* Makes communication clearer.


# The 5W Alert Report Format

A good SOC alert report should answer the **Five W's**.

### 1. Who?

Who performed the activity?

Examples:

* Username
* User account
* Email address
* Process owner

Example:

> User **john.smith** logged into the VPN.


### 2. What?

What suspicious activity occurred?

Examples:

* Phishing email received
* Malware executed
* Multiple failed logins
* PowerShell launched
* Mimikatz executed

Example:

> A phishing email containing a malicious ZIP attachment was opened.


### 3. When?

When did the activity happen?

Include:

* Date
* Time
* Time zone (if applicable)

Example:

> 31 July 2026, 10:15 AM UTC


### 4. Where?

Where did the activity occur?

Examples:

* Hostname
* IP address
* Device name
* Website
* Cloud account

Example:

> Host: FINANCE-PC01
> IP: 192.168.1.25


### 5. Why? (Most Important)

Explain why you reached your verdict.

Mention:

* Evidence collected
* Investigation results
* Why it is malicious or legitimate

-----------
This is a great way to learn SOC reporting. The idea is:

1. **Read the alert.**
2. **Extract the 5W information from the alert.**
3. **Write the final report.**

Below are all **5 examples** in that format.

---

# Example 1 – Malware Execution

## Alert

**Description:**

A malware detection rule triggered because a user downloaded and executed a malicious ZIP attachment. The malware contacted a known Command-and-Control (C2) server after execution.

**User:** john.smith

**Host:** FINANCE-PC01

**IP:** 192.168.1.25

**Downloaded File:** Invoice.zip

**Detection:** Known Malware Hash

**Network Connection:** 45.67.89.100 (Known C2)

---

## Step 1: Extract the 5W's

**Who?**

User **john.smith**

---

**What?**

Downloaded and executed a malicious ZIP attachment.

---

**When?**

31 July 2026, 10:15 AM

---

**Where?**

Host **FINANCE-PC01**
IP **192.168.1.25**

---

**Why?**

The file matched a known malware hash and attempted to communicate with a Command-and-Control server, indicating malware infection.

---

## Final Alert Report

**Who:** User `john.smith`

**What:** Downloaded and executed a malicious ZIP attachment.

**When:** 31 July 2026, 10:15 AM

**Where:** Host `FINANCE-PC01` (192.168.1.25)

**Why:** The downloaded file matched a known malware signature and attempted to communicate with a known Command-and-Control (C2) server.

**Verdict:** True Positive

**Recommendation:** Escalate to L2 for malware removal and endpoint isolation.

---

# Example 2 – Active Directory Discovery

## Alert

**Description**

Detects a spike of commands like **whoami**, **net user**, and **Get-ADUser**, often used during Active Directory reconnaissance.

**Invoked Commands**

```
dir
hostname
whoami /priv
net group "Domain Admins" /domain
nltest /dclist:tryhackme.thm
```

**Host Name**

DMZ-MSEXCHANGE-2013

**Host OS**

Windows Server 2012 R2

**User**

NT AUTHORITY\SYSTEM

**Source Process**

```
C:\Windows\System32\cmd.exe
```

**Parent Process**

```
C:\Users\Public\revshell.exe
```

**Grandparent Process**

```
C:\Windows\System32\inetsrv\w3wp.exe
```

---

## Step 1: Extract the 5W's

**Who?**

User **NT AUTHORITY\SYSTEM**

---

**What?**

Executed multiple Active Directory discovery commands.

---

**When?**

(Alert timestamp from SIEM)

---

**Where?**

Hostname **DMZ-MSEXCHANGE-2013**

---

**Why?**

The commands were launched from a suspicious reverse shell process, indicating post-exploitation reconnaissance.

---

## Final Alert Report

**Who:** User `NT AUTHORITY\SYSTEM`

**What:** Multiple Active Directory discovery commands were executed from the command prompt.

**When:** Alert timestamp.

**Where:** Host `DMZ-MSEXCHANGE-2013` (Windows Server 2012 R2)

**Why:** The reconnaissance commands originated from **revshell.exe**, indicating possible attacker activity after compromising the system.

**Verdict:** True Positive

**Recommendation:** Escalate to L2 for immediate host isolation and incident response.

---

# Example 3 – Phishing Email

## Alert

**Description**

The email was classified as phishing after automated analysis.

**Subject**

Important Update: Microsoft Teams Pricing Increase

**Sender**

Microsoft Support

[support@microsoft.com](mailto:support@microsoft.com)

**Recipient**

Eddie Huffman

IT Manager

**Security Checks**

SPF : Fail

DKIM : Fail

**Attachment**

REPORT.rar

---

## Step 1: Extract the 5W's

**Who?**

Recipient **Eddie Huffman**

---

**What?**

Received a phishing email with a suspicious attachment.

---

**When?**

(Alert timestamp)

---

**Where?**

Recipient mailbox.

---

**Why?**

Failed SPF and DKIM checks together with a suspicious RAR attachment strongly indicate email spoofing and phishing.

---

## Final Alert Report

**Who:** Eddie Huffman (IT Manager)

**What:** Received a phishing email containing a suspicious RAR attachment.

**When:** Alert timestamp.

**Where:** Corporate email mailbox.

**Why:** The sender failed SPF and DKIM authentication, and the email contained social engineering language with a suspicious attachment.

**Verdict:** True Positive

**Recommendation:** Escalate to L2, quarantine the email, analyze the attachment, and identify other recipients.

---

# Example 4 – Web Attack

## Alert

**Description**

Large number of requests matching web attack patterns.

**Source IP**

5.178.87.180

**Targets**

DMZ-WEBFRONT-PRD

DMZ-WEBAPI-PRD

DMZ-MSEXCHANGE-2013

**Request Sample**

```
?id=1' OR '1'='1
```

**Total Requests**

865700

---

## Step 1: Extract the 5W's

**Who?**

Source IP **5.178.87.180**

---

**What?**

Massive SQL Injection attack.

---

**When?**

(Alert timestamp)

---

**Where?**

Corporate web servers.

---

**Why?**

Hundreds of thousands of SQL Injection requests targeted multiple production servers.

---

## Final Alert Report

**Who:** Source IP `5.178.87.180`

**What:** Performed a large-scale SQL Injection attack against multiple web servers.

**When:** Alert timestamp.

**Where:** DMZ-WEBFRONT-PRD, DMZ-WEBAPI-PRD, and DMZ-MSEXCHANGE-2013.

**Why:** The requests matched SQL Injection patterns and reached an unusually high volume, indicating an automated attack.

**Verdict:** True Positive

**Recommendation:** Escalate to L2, block the source IP, review WAF logs, and verify whether any requests were successful.

---

# Example 5 – Data Exfiltration

## Alert

**Description**

A Google Drive document from an important folder was shared outside the organization.

**User**

[m.boslan@tryhackme.thm](mailto:m.boslan@tryhackme.thm)

**Department**

HR

**External Email**

[shadow18562@protonmail.thm](mailto:shadow18562@protonmail.thm)

**File**

Employee Records (Updated)

**Folder**

HR Internal

---

## Step 1: Extract the 5W's

**Who?**

HR user **[m.boslan@tryhackme.thm](mailto:m.boslan@tryhackme.thm)**

---

**What?**

Shared confidential HR data externally.

---

**When?**

(Alert timestamp)

---

**Where?**

Google Drive – HR Internal folder.

---

**Why?**

Sensitive employee records were shared with an external personal email, violating company policy.

---

## Final Alert Report

**Who:** [m.boslan@tryhackme.thm](mailto:m.boslan@tryhackme.thm) (HR Department)

**What:** Shared a confidential HR spreadsheet with an external personal email account.

**When:** Alert timestamp.

**Where:** Google Drive – HR Internal folder.

**Why:** The document containing employee records was shared outside the organization, violating company data handling policies.

**Verdict:** True Positive

**Recommendation:** Escalate to L2, revoke external sharing permissions, investigate whether the action was authorized, and notify the Compliance team.

---

###  SOC Tip for Interviews

When you receive any alert, always think in this order:

**Read the alert → Extract the 5W's → Write the report → Set the verdict → Give a recommendation (if needed).**

---------------------------
# Alert Escalation

After completing your investigation and writing the alert report, the next step is to decide **whether the alert should be escalated to an L2 Analyst**.

Escalation means **passing the alert to a more experienced analyst** because it requires deeper investigation, remediation, or additional expertise.

---

# When Should You Escalate an Alert?

You should escalate an alert to **L2** if any of the following conditions are met.

## 1. Major Cyberattack

If the alert indicates a serious cyberattack that requires deeper investigation or Digital Forensics & Incident Response (DFIR).

### Examples

* Ransomware infection
* Active Directory compromise
* Domain Controller attack
* Credential dumping (Mimikatz)
* Data exfiltration
* Lateral movement
* Command and Control (C2) communication

**Example**

An attacker gains access to an Exchange Server and begins running commands like:

```text
whoami
net group "Domain Admins"
nltest /dclist
```

This suggests the attacker is performing Active Directory reconnaissance.

**Decision:** Escalate to L2 immediately.

---

## 2. Remediation Is Required

If the incident requires actions that L1 analysts are not authorized to perform.

### Examples

* Remove malware
* Isolate infected endpoint
* Reset user passwords
* Disable compromised accounts
* Block malicious IPs
* Quarantine phishing emails

**Example**

A user executes malware that connects to a malicious C2 server.

L1 confirms the infection.

Since the device must be isolated and malware removed, the alert is escalated.

**Decision:** Escalate to L2.

---

## 3. Other Teams Must Be Involved

If resolving the incident requires communication outside the SOC.

Possible departments include:

* IT Team
* HR
* Compliance
* Legal
* Management
* Public Relations (PR)
* Customers
* Law Enforcement

**Example**

An HR employee shares confidential employee records with an external email address.

The Compliance team must investigate whether company policies were violated.

**Decision:** Escalate to L2.

---

## 4. You Are Not Sure

One of the most important rules for a new SOC analyst:

> **Never guess. If you are unsure, ask for help.**

It is completely acceptable for an L1 analyst to request assistance.

**Example**

You receive an alert involving PowerShell, encoded commands, and Windows Registry modifications.

You cannot determine whether it is legitimate administrative activity or malware.

**Decision:** Escalate to L2 for assistance.

---

# Escalation Workflow

```text
Alert Received
       │
       ▼
Investigate Alert
       │
       ▼
Write Alert Report
       │
       ▼
Set Verdict
(True Positive / False Positive)
       │
       ▼
Escalation Needed?
       │
 ┌─────┴─────┐
 │           │
 No          Yes
 │            │
Close      Assign to L2
               │
               ▼
      Notify L2 Analyst
               │
               ▼
     L2 Continues Investigation
```

---

# What Happens After Escalation?

Once the alert reaches L2, they will:

1. Read your alert report.
2. Review the evidence you collected.
3. Validate your verdict.
4. Perform deeper investigation.
5. Communicate with other teams if necessary.
6. Start Incident Response for major incidents.
7. Contact you if they need additional information.

---

# Example

## Alert

A phishing email containing **REPORT.rar** is delivered to the IT Manager.

You investigate and discover:

* SPF failed
* DKIM failed
* Suspicious attachment
* Social engineering language

You conclude it is malicious.

---

## L1 Report

**Verdict:** True Positive

**Recommendation:** Escalate to L2.

---

## L2 Actions

L2 receives the alert and:

* Quarantines the email
* Checks whether other users received it
* Blocks the sender
* Analyzes the attachment
* Resets credentials if the attachment was opened
* Starts Incident Response if users were compromised

---

# Requesting L2 Support

Sometimes an alert is not clearly malicious or benign.

Instead of making assumptions:

* Ask L2 for guidance.
* Explain what you investigated.
* Share your evidence.
* Learn from their feedback.

This improves both the investigation and your skills as an analyst.

-----------------

# Crisis Communication in a SOC

In a real SOC, not every incident follows the normal workflow. Unexpected situations can occur, such as unavailable team members, system failures, or a large number of security alerts. To handle these situations effectively, SOC teams usually have **Crisis Communication Procedures**—documented guidelines that explain who to contact and what actions to take during critical incidents.

As an L1 analyst, you should know how to respond when normal procedures cannot be followed.

---

# Communication Cases

## Case 1: L2 Analyst Is Unavailable

### Scenario

You discover a **critical security incident** and need to escalate it immediately, but the **L2 analyst does not respond for 30 minutes**.

### What Should You Do?

1. Try contacting the L2 analyst.
2. If there is no response, contact the L3 analyst.
3. If L3 is also unavailable, contact your SOC Manager or the emergency contact.
4. Follow your organization's emergency escalation procedure.

### Why?

Critical incidents like ransomware or domain compromise cannot wait. Delaying the response gives attackers more time to cause damage.

**Example**

You detect ransomware spreading across multiple servers, but L2 is unavailable.

**Correct Action:**

Call L2 → L3 → SOC Manager until someone responds.

---

## Case 2: Compromised Slack or Microsoft Teams Account

### Scenario

An alert indicates that a user's Slack or Microsoft Teams account has been compromised.

You need to verify whether the login was legitimate.

### Wrong Approach

❌ Send a message through the compromised Slack or Teams account.

The attacker may have access to those messages.

### Correct Approach

Use another trusted communication method.

Examples:

* Phone call
* SMS
* Personal email (if allowed)
* Face-to-face conversation

### Why?

The compromised account cannot be trusted for verification.

**Example**

Microsoft Defender reports that John's Teams account was accessed from another country.

Instead of asking John on Teams,

✔ Call John directly.

---

## Case 3: Too Many Alerts

### Scenario

Thousands of alerts suddenly appear in the SOC dashboard.

Some are Low severity, while others are Critical.

### What Should You Do?

1. Follow the SOC prioritization process.
2. Investigate Critical alerts first.
3. Continue with High, Medium, and Low alerts.
4. Inform your L2 analyst that the SOC is experiencing a high alert volume.

### Why?

Critical threats should never be delayed because of less important alerts.

**Example**

You receive:

* 5 Critical alerts
* 40 High alerts
* 500 Medium alerts
* 3000 Low alerts

Start with the Critical alerts and notify L2 about the alert surge.

---

## Case 4: You Misclassified an Alert

### Scenario

A few days later, you realize that an alert you closed as a False Positive was actually malicious.

### What Should You Do?

1. Inform your L2 analyst immediately.
2. Explain what you found.
3. Reopen the investigation if required.
4. Assist in any follow-up investigation.

### Why?

Attackers often remain inside a network for days or weeks before causing noticeable damage. Reporting the mistake quickly can reduce the impact.

**Example**

You initially closed a PowerShell alert as legitimate.

Three days later, new evidence shows it downloaded malware.

Immediately notify L2 and explain the new findings.

---

## Case 5: SIEM Logs Are Missing or Broken

### Scenario

The SIEM is not parsing logs correctly, or the logs are unavailable.

Without logs, you cannot complete the investigation.

### What Should You Do?

1. Investigate using any available evidence.
2. Do **not** ignore or close the alert because logs are missing.
3. Report the logging issue to the L2 analyst or SOC Engineer.
4. Document the limitation in your alert report.

### Why?

Missing logs do not mean the alert is a False Positive. There may still be a real security incident.

**Example**

A malware alert is generated, but the endpoint logs are unavailable due to an ingestion failure.

Continue investigating using EDR data or firewall logs, and report the SIEM issue.

---

# Communication by L2

After receiving an escalated alert, the L2 analyst may need to coordinate with multiple departments depending on the incident.

Examples include:

| Incident          | Teams Involved                    |
| ----------------- | --------------------------------- |
| Malware infection | IT Operations                     |
| Data leak         | Compliance, Legal                 |
| Public breach     | Public Relations (PR), Management |
| Insider threat    | HR, Management                    |
| Large cyberattack | Incident Response (DFIR) Team     |
| Customer impact   | Customer Support, Management      |

### Example

An HR employee shares confidential employee records with an external email account.

After L1 escalates the alert:

* L2 validates the incident.
* L2 starts a **DFIR (Digital Forensics and Incident Response)** investigation.
* L2 contacts the **Compliance Team** to investigate the policy violation.
* If required, **Legal** is informed to assess regulatory obligations.
* If the incident becomes public, **PR** prepares official communication.

---

# Best Practices for L1 Analysts

* Always follow your organization's escalation procedures.
* Never delay reporting a critical incident.
* Use trusted communication methods if an account is compromised.
* Prioritize alerts based on severity.
* Admit mistakes quickly—early reporting can prevent larger incidents.
* Never ignore an alert because logs are incomplete or unavailable.
* When in doubt, ask for help from L2 rather than making assumptions.

---

