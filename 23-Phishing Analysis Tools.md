# Email Analysis Artifacts

When analyzing a suspicious email, your **first job** is to collect evidence (artifacts).

Artifacts help you answer these questions:

* Is the email legitimate?
* Is it a phishing email?
* What is the attacker's goal?

These artifacts can then be used for:

* Reputation checks
* Threat intelligence lookups
* Malware analysis
* Incident investigations

---

# 1. Header Artifacts

The **email header** contains technical information about the email.

---

## Sender Email Address

**Question:**

```text
Who sent the email?
```

---

**Example:**

```text
Display name:

Netflix Billing
```

```text
Actual email:

z99@musacomb.online
```

---

**What should you check?**

* Does the domain match the company?
* Is the sender spoofed?

---

## Sender IP Address

**Question:**

```text
Where did the email come from?
```

---

**Example:**

```text
Originating IP:

185.224.128.17
```

---

**What should you check?**

* Which country does the IP belong to?
* Does a reverse lookup reveal anything suspicious?

---

## Email Subject Line

**Question:**

```text
Does the subject create urgency?
```

---

**Examples:**

```text
Your account will be locked!
```

```text
Action Required!
```

```text
Your recent purchase
```

---

**What should you check?**

* Urgent language
* Threatening messages
* Unexpected notifications

---

## Recipient Email Address

**Question:**

```text
Who received the email?
```

---

**Check these fields:**

```text
To:

CC:

BCC:
```

---

**Example:**

```text
BCC: victim@yahoo.com
```

---

**Why is this important?**

BCC often indicates a mass phishing campaign.

---

## Reply-To Address

**Question:**

```text
Where will replies be sent?
```

---

**Example:**

```text
From:

support@company.com
```

```text
Reply-To:

attacker@gmail.com
```

---

**Why is this suspicious?**

The reply address is different from the sender's address.

---

## Date and Time

**Question:**

```text
When was the email sent?
```

---

**Example:**

```text
Thursday, July 15, 2021
```

---

**What should you check?**

* Is the email sent at an unusual time?
* Does the timestamp match the sender's location?

---

# 2. Body Analysis

The **email body** contains the actual message.

---

## URLs and Hyperlinks

**Question:**

```text
Which links are included?
```

---

**Example:**

```text
Claim Your Hat!
```

---

**What should you check?**

* Expand shortened URLs.
* Inspect the destination.
* Defang suspicious URLs.

---

**Example:**

```text
Original:

http://malicioussite.com
```

```text
Defanged:

hxxp[://]malicioussite[.]com
```

---

## Attachment Names

**Question:**

```text
Which files are attached?
```

---

**Examples:**

```text
invoice.pdf
```

```text
document.docx
```

```text
shipment.xlsx
```

---

**What should you check?**

* File extension
* Unusual file names
* Double extensions

---

**Example:**

```text
invoice.pdf.exe
```

---

**Why is this suspicious?**

Attackers often disguise executable files as documents.

---

## Attachment Hash

**Question:**

```text
What is the file's hash value?
```

---

**Examples:**

```text
MD5
```

```text
SHA1
```

```text
SHA256
```

---

**Why do we calculate hashes?**

Hashes help us:

* Identify known malware
* Search threat intelligence databases
* Compare files

---

# Email Analysis Checklist

```text
HEADER ANALYSIS

✓ Sender email address

✓ Sender IP address

✓ Subject line

✓ Recipient address

✓ Reply-To address

✓ Date and time
```

---

```text
BODY ANALYSIS

✓ URLs

✓ Hyperlinks

✓ Attachments

✓ File extensions

✓ File hashes
```

---------------------


# Email Analysis Learning Lab 

If you're in the **learning phase**, don't just read about the tools. Use **every tool** on the **same email** and compare the results.

---

# Step 0: Get a Sample Email

You need one of these:

* A suspicious email from your inbox.
* A phishing email from a training platform.
* A `.eml` sample file.

---

# Step 1: Open the Raw Email

## Gmail

1. Open the email.
2. Click **⋮ (More)**.
3. Click **Show original**.
4. Copy the entire header.

You should see something like this:

```text
Delivered-To: user@gmail.com

Received: from mail.fake-login.xyz (203.0.113.10)

From: Microsoft Support <support@microsoft.com>

Reply-To: help@fake-login.xyz

Subject: Your account will be suspended

Authentication-Results:
    spf=fail
    dkim=fail
    dmarc=fail

Message-ID:
    <123456@mail.fake-login.xyz>
```

---

# Tool 1: Google Messageheader

## Purpose

Analyze the email header.

## Website

[Google Messageheader Tool](https://toolbox.googleapps.com/apps/messageheader/?utm_source=chatgpt.com)

## Steps

1. Copy the entire header.
2. Open the tool.
3. Paste the header.
4. Click **Analyze Header**.

## What to learn

| Field     | What to check                  |
| --------- | ------------------------------ |
| From      | Is the sender legitimate?      |
| Subject   | Is there urgency?              |
| SPF       | Pass or Fail?                  |
| DKIM      | Pass or Fail?                  |
| DMARC     | Pass or Fail?                  |
| Sender IP | Where did the email come from? |

---

# Tool 2: Message Header Analyzer

## Purpose

Analyze the email route (mail hops).

## Website

[Microsoft Message Header Analyzer](https://mha.azurewebsites.net/?utm_source=chatgpt.com)

## Steps

1. Copy the email header.
2. Open the tool.
3. Paste the header.
4. Click **Analyze Headers**.

## What to learn

### Mail hops

```text
Attacker
     ↓

Mail Server 1
     ↓

Mail Server 2
     ↓

Gmail
     ↓

Your Inbox
```

### Delays

```text
1 second → Normal

30 minutes → Investigate
```

### Received headers

Look for:

```text
Received:
```

Example:

```text
Received: from mail.fake-login.xyz (203.0.113.10)
```

---

# Tool 3: IPInfo

## Purpose

Investigate the sender's IP address.

## Website

[IPInfo](https://ipinfo.io/?utm_source=chatgpt.com)

## Steps

1. Find the IP address.

```text
203.0.113.10
```

2. Copy it.
3. Open IPInfo.
4. Paste the IP.
5. Click **Search**.

## What to learn

| Field        | Example     |
| ------------ | ----------- |
| Country      | US          |
| City         | Kansas City |
| Organization | Google LLC  |
| ASN          | AS15169     |

## Questions

```text
Who owns the IP?

Does the country make sense?

Does the organization make sense?
```

---

# Tool 4: URLScan

## Purpose

Analyze URLs safely.

## Website

[URLScan](https://urlscan.io/?utm_source=chatgpt.com)

## Example

Suppose the email contains:

```text
https://bit.ly/payment
```

## Steps

1. Copy the URL.
2. Open URLScan.
3. Paste the URL.
4. Click **Scan**.

## What to learn

### Redirects

```text
bit.ly
   ↓

fake-login.xyz
```

### Screenshot

See what the website looks like.

### Requests

Check whether the website contacts other domains.

---

# Tool 5: Talos Intelligence

## Purpose

Check the reputation of an IP address or domain.

## Website

[Cisco Talos Intelligence Center](https://talosintelligence.com/?utm_source=chatgpt.com)

## Steps

1. Copy the IP address.

```text
203.0.113.10
```

or the domain:

```text
fake-login.xyz
```

2. Open Talos.
3. Paste the IP or domain.
4. Click **Search**.

## What to learn

| Check      | Meaning                |
| ---------- | ---------------------- |
| Reputation | Good, Neutral, or Poor |
| Blacklists | Is the IP listed?      |
| Owner      | Who owns it?           |

---

# Tool 6: WHOIS

## Purpose

Find information about a domain.

## Website

[WHOIS Lookup by ICANN](https://lookup.icann.org/?utm_source=chatgpt.com)

## Example

```text
fake-login.xyz
```

## Steps

1. Copy the domain.
2. Open the website.
3. Paste the domain.
4. Search.

## What to learn

```text
Domain creation date

Registrar

Registration details
```

## Red flag

```text
paypal.com → Registered many years ago

paypal-security-2026.xyz → Registered 2 days ago
```

---

# Tool 7: VirusTotal

## Purpose

Check files, URLs, domains, and IP addresses.

## Website

[VirusTotal](https://www.virustotal.com/?utm_source=chatgpt.com)

## Steps

### URL analysis

1. Copy the URL.
2. Paste it into VirusTotal.
3. Click **Search**.

### File analysis

1. Download the `.eml` file.
2. Upload it.
3. Review the results.

## What to learn

```text
Detection ratio

Malicious domains

Relationships

Community comments
```

---

# Investigation Report Template

```text
Subject:
________________________________

Sender:
________________________________

Reply-To:
________________________________

SPF:
________________________________

DKIM:
________________________________

DMARC:
________________________________

Sender IP:
________________________________

Country:
________________________________

Suspicious URL:
________________________________

Talos Reputation:
________________________________

VirusTotal Result:
________________________________

Final Verdict:

Legitimate / Suspicious / Phishing
```

**My suggestion:** Use **one email and run it through all seven tools.** You'll understand phishing analysis much faster than by reading theory alone.


---------------------


The **email body** is the part of the email that the user actually sees. This is usually where attackers place their **malicious links** or **malicious attachments**.

# Mail Body Analysis Workflow

```text
Open the email
        ↓
Read the email body
        ↓
Find all links
        ↓
Analyze the links
        ↓
Check for attachments
        ↓
Generate the file hash
        ↓
Check the file reputation
        ↓
Decide whether the email is malicious
```

---

# Tool 1: Copy Link Address

## Purpose

Extract a URL **without opening it**.

---

## How to use it

### Step 1

Open the email.

---

### Step 2

Find the hyperlink.

Example:

```text
Verify your account
```

---

### Step 3

**Do not click the link.**

Right-click it.

---

### Step 4

Select:

```text
Copy link address
```

or

```text
Copy hyperlink
```

---

### Step 5

Paste it into a text editor.

Example:

```text
Displayed text:

Verify your account

↓

Actual URL:

https://paypal-login-update.xyz
```

---

## What to look for

```text
paypal.com → Legitimate

paypal-login-update.xyz → Suspicious
```

---

# Tool 2: URL Extractor

## Purpose

Extract **all URLs** from an email automatically.

---

## Website

[ConvertCSV URL Extractor](https://www.convertcsv.com/url-extractor.htm?utm_source=chatgpt.com)

---

## How to use it

### Step 1

Open the email.

---

### Step 2

Copy the email body or the raw email source.

---

### Step 3

Open the URL extractor.

---

### Step 4

Paste the content.

Example:

```html
<a href="https://fake-login.xyz">
Verify Account
</a>

<a href="https://payment.xyz">
Update Payment
</a>
```

---

### Step 5

Click:

```text
Extract
```

---

### Output

```text
https://fake-login.xyz

https://payment.xyz
```

---

## What to look for

* Hidden URLs
* Multiple URLs
* Shortened URLs

Example:

```text
bit.ly

tinyurl.com

t.co
```

---

# Tool 3: CyberChef

## Purpose

Extract hidden or obfuscated URLs.

---

## Website

[CyberChef URL Extraction Recipe](https://gchq.github.io/CyberChef/?utm_source=chatgpt.com#recipe=Extract_URLs%28false,false,false%29)

---

## How to use it

### Step 1

Copy the raw email.

---

### Step 2

Open CyberChef.

---

### Step 3

Paste the email into the **Input** panel.

---

### Step 4

The **Extract URLs** recipe will automatically identify URLs.

---

### Output

```text
https://fake-login.xyz

https://download.xyz

https://tracking.xyz
```

---

## What to look for

```text
Tracking links

Download links

Login pages

Redirects
```

---

# Tool 4: SHA256 (Linux)

## Purpose

Generate a unique fingerprint for an attachment.

---

## Important

**Never download an attachment on your personal computer.**

Use:

```text
Virtual machine

Sandbox

Lab environment
```

---

## Example attachment

```text
invoice.pdf
```

---

## How to use it

### Step 1

Download the attachment in a lab environment.

---

### Step 2

Open the terminal.

---

### Step 3

Run:

```bash
sha256sum invoice.pdf
```

---

### Output

```text
025ba9ce4a2118a9ca7b115c8869ff73bc16bad3732ba359cef1e60ad8f961f9
```

---

## What to learn

The hash is the file's **digital fingerprint**.

If two files have the same SHA256 value:

```text
File A = File B
```

---

# Tool 5: Talos

## Purpose

Check the attachment's reputation.

---

## Website

[Cisco Talos Intelligence Center](https://talosintelligence.com/reputation_center/?utm_source=chatgpt.com)

---

## How to use it

### Step 1

Copy the SHA256 hash.

---

### Step 2

Open Talos.

---

### Step 3

Paste the hash.

---

### Step 4

Click:

```text
Search
```

---

## What to look for

```text
Malicious

Phishing

Spam
```

---

# Tool 6: VirusTotal

## Purpose

Analyze files, URLs, IP addresses, domains, and hashes.

---

## Website

[VirusTotal](https://www.virustotal.com/gui/?utm_source=chatgpt.com)

---

## How to analyze a file

### Step 1

Copy the SHA256 hash.

---

### Step 2

Open VirusTotal.

---

### Step 3

Paste the hash.

---

### Step 4

Click:

```text
Search
```

---

## Example result

```text
25/70 security vendors detected malware
```

---

## How to analyze a URL

### Step 1

Copy the URL.

---

### Step 2

Paste it into VirusTotal.

---

### Step 3

Review the results.

---

## What to look for

```text
Detection ratio

Community comments

Relationships

Behavior
```

---

# Complete Email Body Analysis Workflow

```text
Read the email body
          ↓
Extract the links
          ↓
Copy the URLs
          ↓
URL Extractor
          ↓
CyberChef
          ↓
URLScan
          ↓
Download the attachment (lab only)
          ↓
Generate the SHA256 hash
          ↓
Talos
          ↓
VirusTotal
          ↓
Write the report
```

---

# SOC Analyst Investigation Report

```text
Subject:
________________________

Suspicious URL:
________________________

Hidden URLs:
________________________

Attachment:
________________________

SHA256:
________________________

VirusTotal:
________________________

Talos:
________________________

Final Verdict:

Legitimate

Suspicious

Phishing

Malware
```

---

# Tools Summary

| Tool              | Purpose                |
| ----------------- | ---------------------- |
| Copy Link Address | Extract a URL safely   |
| URL Extractor     | Extract all URLs       |
| CyberChef         | Extract hidden URLs    |
| SHA256            | Generate a file hash   |
| Talos             | Check file reputation  |
| VirusTotal        | Analyze files and URLs |

**For practice, analyze the same email with all 6 tools and compare the results. This is very similar to what a SOC analyst does during phishing investigations.**

---------------------------

# Malware Attachment Analysis 

When an email contains an attachment, **don't open it on your own computer.**

Instead of analyzing the file manually, security analysts upload it to a **malware sandbox**.

A **sandbox** is an isolated environment that safely runs suspicious files.

---

# Malware Analysis Workflow

```text
Suspicious email
        ↓
Download the attachment (lab only)
        ↓
Upload the file to a malware sandbox
        ↓
Execute the file
        ↓
Observe its behavior
        ↓
Collect IOCs
        ↓
Write the report
```

---

# What is an IOC?

**IOC = Indicator of Compromise**

These are pieces of evidence that indicate malicious activity.

Examples:

```text
Malicious URL

Malicious IP address

Suspicious domain

Downloaded payload

Registry modification

Created files

Running processes
```

---

# Tool 1: ANY.RUN

## Purpose

Analyze suspicious files and URLs **interactively**.

---

## Website

[ANY.RUN](https://app.any.run/?utm_source=chatgpt.com)

---

## How to use it

### Step 1

Open ANY.RUN.

---

### Step 2

Upload the attachment.

Example:

```text
invoice.pdf
```

---

### Step 3

Start the analysis.

---

### Step 4

Watch the malware execute.

---

## What to look for

### Processes

```text
winword.exe
        ↓
powershell.exe
        ↓
cmd.exe
```

A document that launches PowerShell is suspicious.

---

### Network activity

```text
10.0.0.15
        ↓
malicious-server.xyz
```

Check which domains the malware contacts.

---

### Downloaded files

```text
invoice.pdf
        ↓
payload.exe
```

Did the attachment download another file?

---

### Indicators of compromise

```text
IP addresses

Domains

URLs

File hashes
```

---

## Why analysts like ANY.RUN

```text
Interactive

Real-time monitoring

Easy to understand
```

---

# Tool 2: Hybrid Analysis

## Purpose

Analyze suspicious files automatically.

---

## Website

[Hybrid Analysis](https://hybrid-analysis.com/?utm_source=chatgpt.com)

---

## How to use it

### Step 1

Upload the suspicious file.

---

### Step 2

Wait for the analysis to finish.

---

### Step 3

Review the report.

---

## What to look for

### File activity

```text
Created files

Deleted files

Modified files
```

---

### Network activity

```text
Connections

Domains

URLs

IP addresses
```

---

### Threat score

```text
Low

Medium

High
```

---

## Why analysts use Hybrid Analysis

```text
Free

Detailed reports

Easy IOC extraction
```

---

# Tool 3: JOESandbox

## Purpose

Perform **static** and **dynamic** malware analysis.

---

## Website

[JOESandbox Cloud](https://www.joesandbox.com/?utm_source=chatgpt.com)

---

# Static Analysis

The file is **not executed**.

The sandbox examines:

```text
File name

Strings

Metadata

Imported libraries

Embedded URLs
```

Example:

```text
invoice.pdf
        ↓
Embedded URL found
        ↓
http://malicious-download.xyz
```

---

# Dynamic Analysis

The file **is executed**.

The sandbox monitors:

```text
Processes

Registry changes

Network activity

Created files

Downloaded payloads
```

Example:

```text
invoice.pdf
        ↓
cmd.exe
        ↓
powershell.exe
        ↓
malware.exe
```

---

# Comparison

| Tool            | Best for             | Special feature           |
| --------------- | -------------------- | ------------------------- |
| ANY.RUN         | Interactive analysis | Real-time monitoring      |
| Hybrid Analysis | Automatic analysis   | Detailed reports          |
| JOESandbox      | Advanced analysis    | Static + dynamic analysis |

---

# Example Investigation

```text
Email attachment:

invoice.pdf
```

---

## Upload to ANY.RUN

```text
invoice.pdf
        ↓
powershell.exe starts
        ↓
Connects to evil-server.xyz
```

---

## Upload to Hybrid Analysis

```text
Threat score: High

Downloads another file

Creates a registry entry
```

---

## Upload to JOESandbox

```text
Static analysis:

Embedded URL detected

Dynamic analysis:

Network communication detected
```

---

# Complete SOC Workflow

```text
Suspicious email
        ↓
Download the attachment
        ↓
Calculate the SHA256 hash
        ↓
VirusTotal
        ↓
ANY.RUN
        ↓
Hybrid Analysis
        ↓
JOESandbox
        ↓
Collect IOCs
        ↓
Write the report
```

-----------------------

# PhishTool 

**PhishTool** is an **all-in-one phishing investigation platform**.

Instead of using **5–10 separate tools**, PhishTool combines everything into a **single dashboard**.

---

## Website

[PhishTool](https://www.phishtool.com/?utm_source=chatgpt.com)

---

# Why Use PhishTool?

Without PhishTool:

```text id="wf2rvz"
Email
   ↓
Message Header Analyzer
   ↓
IPInfo
   ↓
URLScan
   ↓
VirusTotal
   ↓
Talos
   ↓
Investigation Report
```

With PhishTool:

```text id="rhrbgm"
Email
   ↓
PhishTool
   ↓
Investigation Report
```

---

# Who Uses It?

| Role                        | Purpose                        |
| --------------------------- | ------------------------------ |
| SOC Analyst                 | Investigate reported emails    |
| Threat Intelligence Analyst | Collect indicators             |
| Security Researcher         | Investigate phishing campaigns |

---

# Step 1: Upload the Email

You can upload:

```text id="6m5lb7"
.eml file

.msg file

Raw email source
```

---

# Step 2: Analyze the Email Body

PhishTool displays the email exactly as it appears in the inbox.

---

## Rendered HTML

Shows:

```text id="q1rj7q"
Buttons

Images

Links

Formatting
```

Example:

```text id="vl0y0z"
Click here to verify your account
```

---

## Raw HTML

Shows the underlying HTML code.

Example:

```html id="p3y08d"
<a href="https://fake-login.xyz">
Verify your account
</a>
```

This helps you discover **hidden URLs**.

---

## Message Source

Shows:

```text id="if64cu"
Headers

Routing information

Authentication results
```

---

# Step 3: Analyze the Authentication Results

Check:

```text id="u9md2o"
SPF

DKIM

DMARC
```

Example:

```text id="xuh0rw"
SPF = Fail

DKIM = Fail

DMARC = Fail
```

---

# Step 4: Analyze the Transmission Path

Every mail server adds a **Received** header.

Example:

```text id="ybzwl4"
Attacker
     ↓

Mail Server 1
     ↓

Mail Server 2
     ↓

Gmail
     ↓

Inbox
```

---

# Step 5: Analyze Embedded URLs

PhishTool automatically extracts URLs.

Example:

```text id="nuxesn"
Visible text:

Verify your account

↓

Actual URL:

https://fake-login.xyz
```

---

# Step 6: Analyze Attachments

If an attachment exists:

```text id="jljlwm"
invoice.pdf

document.docx

report.zip
```

PhishTool can extract and analyze the attachment.

---

# Step 7: VirusTotal Integration

One of PhishTool's best features is its **VirusTotal integration**.

You don't have to switch between tools.

---

## Example

```text id="s0m2l5"
URL:

fake-login.xyz
        ↓
VirusTotal
        ↓
20/70 vendors detected malware
```

---

# Step 8: Collect Artifacts

## What are artifacts?

Artifacts are pieces of evidence found during an investigation.

---

## Examples

```text id="m94fow"
Sender address

Reply-To address

Sender IP

Domain

URL

File hash

Attachment
```

---

# Step 9: Write Investigation Notes

Example:

```text id="4j4fml"
Sender address spoofed.

SPF failed.

DKIM failed.

Embedded URL redirects to a phishing website.

Attachment flagged as malicious.
```

---

# Step 10: Resolve the Case

Mark the email as:

```text id="l0jupg"
Legitimate

Suspicious

Malicious
```

Click:

```text id="xc7fez"
Resolve
```

This simulates a **real SOC case closure**.

---

# Real SOC Workflow

```text id="ctyecw"
User reports a suspicious email
              ↓
Upload the email to PhishTool
              ↓
Analyze the headers
              ↓
Analyze the URLs
              ↓
Analyze the attachments
              ↓
Check VirusTotal
              ↓
Collect IOCs
              ↓
Write investigation notes
              ↓
Mark the email as malicious
              ↓
Resolve the case
```

---

# Artifacts to Collect

| Artifact   | Example                                                 |
| ---------- | ------------------------------------------------------- |
| Sender     | [support@fake-login.xyz](mailto:support@fake-login.xyz) |
| IP Address | 203.0.113.10                                            |
| Domain     | fake-login.xyz                                          |
| URL        | [https://fake-login.xyz](https://fake-login.xyz)        |
| Attachment | invoice.pdf                                             |
| SHA256     | 025ba9ce...                                             |
| SPF        | Fail                                                    |
| DKIM       | Fail                                                    |
| DMARC      | Fail                                                    |

------------------------------

