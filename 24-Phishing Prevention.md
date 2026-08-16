# SPF (Sender Policy Framework) 

SPF is one of the **most important email authentication methods**.

Its job is simple:

> **"Is this mail server allowed to send emails for this domain?"**

---

# Simple Example

Suppose Google owns the domain:

```text
gmail.com
```

Google publishes an SPF record that says:

```text
Only these servers can send email for gmail.com.
```

---

## Legitimate Email

```text
Sender:
   ↓

Google Mail Server
   ↓

Recipient's Mail Server
   ↓

SPF Check
   ↓

IP found in the SPF record
   ↓

SPF = PASS
   ↓

Deliver the email
```

---

## Phishing Email

```text
Attacker
   ↓

Fake Mail Server
   ↓

Recipient's Mail Server
   ↓

SPF Check
   ↓

IP not found in the SPF record
   ↓

SPF = FAIL
   ↓

Reject or flag the email
```

---

# SPF Workflow (Based on Your Diagram)

```text
Sender
   ↓

Sending Organization's Mail Server
   ↓

Email Transfer
   ↓

Recipient's Mail Server
   ↓

DNS Lookup
   ↓

SPF Record Verification
   ↓

PASS ✓ → Deliver

FAIL ✗ → Reject or mark as spam
```

---

# Where Is the SPF Record Stored?

SPF records are stored in **DNS** as **TXT records**.

Example:

```text
gmail.com
        ↓

DNS
        ↓

TXT Record
        ↓

SPF Information
```

---

# SPF Results

| Result    | Action             |
| --------- | ------------------ |
| Pass      | Accept the email   |
| Neutral   | Accept the email   |
| None      | Accept the email   |
| SoftFail  | Mark as suspicious |
| PermError | Mark as suspicious |
| Fail      | Reject the email   |
| TempError | Reject the email   |

---

# Understanding an SPF Record

Example:

```text
v=spf1 ip4:127.0.0.1 include:_spf.google.com -all
```

Let's break it down.

---

## v=spf1

```text
v=spf1
```

Meaning:

```text
This is an SPF record.
```

---

## ip4:127.0.0.1

```text
ip4:127.0.0.1
```

Meaning:

```text
127.0.0.1 is allowed to send email.
```

You can authorize multiple IP addresses.

Example:

```text
ip4:192.168.1.10

ip4:203.0.113.25
```

---

## include:_spf.google.com

```text
include:_spf.google.com
```

Meaning:

```text
Trust all mail servers authorized by Google.
```

Instead of listing every Google server manually, the domain simply says:

```text
Include Google's SPF rules.
```

---

## -all

```text
-all
```

Meaning:

```text
Reject every unauthorized sender.
```

---

# Real Example (TryHackMe)

```text
v=spf1 include:_spf.google.com include:email.chargebee.com include:7168674.spf05.hubspotemail.net ~all
```

---

## _spf.google.com

```text
include:_spf.google.com
```

Meaning:

```text
Allow Google's mail servers.
```

---

## email.chargebee.com

```text
include:email.chargebee.com
```

Meaning:

```text
Allow Chargebee's mail servers.
```

---

## 7168674.spf05.hubspotemail.net

```text
include:7168674.spf05.hubspotemail.net
```

Meaning:

```text
Allow HubSpot's mail servers.
```

---

## ~all

```text
~all
```

Meaning:

```text
Allow the email, but mark unauthorized senders as suspicious.
```

---

# Difference Between `~all` and `-all`

| Record | Meaning   |
| ------ | --------- |
| `~all` | Soft fail |
| `-all` | Hard fail |

---

## Soft Fail (`~all`)

```text
SPF = SoftFail
        ↓

Accept the email
        ↓

Mark it as suspicious
```

---

## Hard Fail (`-all`)

```text
SPF = Fail
        ↓

Reject the email
```

---

# SPF Surveyor Tool

## Purpose

Visualize SPF records.

## Website

[SPF Surveyor](https://dmarcian.com/spf-survey/?utm_source=chatgpt.com)

---

## How to Use It

### Step 1

Copy a domain.

Example:

```text
tryhackme.com
```

---

### Step 2

Open SPF Surveyor.

---

### Step 3

Enter the domain.

---

### Step 4

Click **Survey**.

---

### Step 5

Review the SPF record.

Example:

```text
v=spf1 include:_spf.google.com include:email.chargebee.com ~all
```

---

# Google Messageheader Tool

## Purpose

Check SPF results from an email header.

## Website

[Google Messageheader Tool](https://toolbox.googleapps.com/apps/messageheader/?utm_source=chatgpt.com)

---

## How to Use It

### Step 1

Open Gmail.

### Step 2

Click:

```text
Show original
```

### Step 3

Copy the header.

### Step 4

Paste it into the Messageheader tool.

### Step 5

Click:

```text
Analyze Header
```

---

## Example Result

```text
SPF:

SoftFail with IP Unknown
```

Meaning:

```text
The sending server wasn't found in the authorized server list.
```

The receiving mail server couldn't verify the sender.

---

# What a SOC Analyst Checks

```text
Email
    ↓

From address
    ↓

SPF
    ↓

DKIM
    ↓

DMARC
    ↓

Sender IP
    ↓

Verdict
```

------------------------
# DKIM (DomainKeys Identified Mail) — Simple Notes

**DKIM verifies that an email was actually sent by the claimed domain and that the message was not changed during transmission.**

Think of DKIM as a **digital signature for email**.

---

# Simple Example

Suppose you receive an email that says:

```text
From: amazon.com
```

How does your mail server know that the email really came from Amazon?

DKIM solves this problem.

---

# Real-Life Example

Imagine you write a letter and sign it.

```text
You write a letter
        ↓
You sign it
        ↓
The recipient compares the signature
        ↓
Signature matches
        ↓
The letter is authentic
```

DKIM works in the same way.

---

# DKIM Workflow (Based on Your Diagram)

```text
The organization publishes a public key in DNS
                    ↓
The mail server signs the email with a private key
                    ↓
The email is sent
                    ↓
The recipient's mail server receives it
                    ↓
The recipient retrieves the public key from DNS
                    ↓
The signature is verified
                    ↓
Match ✓ → Deliver the email

No match ✗ → Reject or flag the email
```

---

# How DKIM Works (Step by Step)

## Step 1: The domain creates two keys

```text
Private key

Public key
```

---

## Step 2: The public key is stored in DNS

```text
example.com
        ↓

DNS Server
        ↓

Public Key
```

---

## Step 3: The private key signs the email

```text
Private Key
        ↓

Digital Signature
        ↓

Email
```

---

## Step 4: The email is sent

```text
Sender
        ↓

Mail Server
        ↓

Internet
        ↓

Recipient
```

---

## Step 5: The recipient retrieves the public key

```text
DNS
        ↓

Public Key
```

---

## Step 6: The recipient verifies the signature

```text
Private Key Signature
           ↓

Public Key Verification
```

---

## Step 7: Compare the keys

```text
Signature matches
        ↓

DKIM = PASS
```

or

```text
Signature doesn't match
        ↓

DKIM = FAIL
```

---

# DKIM Record

Example:

```text
v=DKIM1; k=rsa; p=<public_key>
```

Let's break it down.

---

# v=DKIM1

```text
v=DKIM1
```

Meaning:

```text
This is a DKIM record.
```

---

# k=rsa

```text
k=rsa
```

Meaning:

```text
The RSA algorithm is used.
```

**RSA** is an encryption algorithm.

---

# p=<public_key>

```text
p=<public_key>
```

Meaning:

```text
This is the public key stored in DNS.
```

The recipient uses this key to verify the signature.

---

# DKIM Results

| Result    | Meaning                         |
| --------- | ------------------------------- |
| Pass      | The signature is valid          |
| Fail      | The signature is invalid        |
| PermError | A permanent DKIM problem exists |
| TempError | A temporary error occurred      |
| None      | No DKIM record was found        |

---

# DKIM = PASS

Example:

```text
Authentication-Results:

dkim=pass
```

Meaning:

```text
✓ The email wasn't modified.

✓ The signature is valid.

✓ The domain is trusted.
```

---

# DKIM = FAIL

Example:

```text
Authentication-Results:

dkim=fail
```

Meaning:

```text
✗ The signature is invalid.

✗ The email may have been modified.

✗ The message is suspicious.
```

---

# DKIM = PERMERROR

Your example shows:

```text
dkim=permerror
```

**PermError = Permanent Error**

Possible causes:

```text
Invalid signature

Missing DNS record

Incorrect DNS configuration

Modified email

Incorrect DKIM setup
```

---

# Example Email Header

```text
Received-SPF:
    pass

Authentication-Results:

    dkim=permerror

    spf=pass

    dmarc=fail
```

---

# How to Interpret This

```text
SPF = PASS
        ↓

The mail server is authorized.
```

---

```text
DKIM = PERMERROR
        ↓

The signature couldn't be verified.
```

---

```text
DMARC = FAIL
        ↓

The overall authentication failed.
```

---

# SPF vs DKIM

| SPF                         | DKIM                         |
| --------------------------- | ---------------------------- |
| Verifies the sending server | Verifies the email signature |
| Uses IP addresses           | Uses cryptographic keys      |
| Stored as a DNS TXT record  | Stored as a DNS TXT record   |
| Can fail after forwarding   | Usually survives forwarding  |

---

# Why Is DKIM Better Than SPF?

Suppose this email is forwarded:

```text
Amazon
     ↓

Your work email
     ↓

Your Gmail account
```

The forwarding server changes.

```text
SPF → May fail

DKIM → Usually passes
```

This is why DKIM is often considered more reliable.

---

# DKIM Record Checker

## Tool

[DKIM Record Checker and Validator](https://dmarcian.com/dkim-inspector/?utm_source=chatgpt.com)

---

# How to Use It

## Step 1

Copy the domain.

Example:

```text
gmail.com
```

---

## Step 2

Open the DKIM checker.

---

## Step 3

Enter the domain.

---

## Step 4

Analyze the results.

---

# Google Messageheader Tool

## Tool

[Google Messageheader Tool](https://toolbox.googleapps.com/apps/messageheader/?utm_source=chatgpt.com)

---

# How to Check DKIM in an Email

```text
Open Gmail
       ↓

Show original
       ↓

Copy the header
       ↓

Paste it into Messageheader
       ↓

Analyze Header
       ↓

Check the DKIM result
```

---

# Real SOC Workflow

```text
Suspicious email
         ↓

Check the sender
         ↓

Check SPF
         ↓

Check DKIM
         ↓

Check DMARC
         ↓

Analyze URLs
         ↓

Analyze attachments
         ↓

Collect IOCs
         ↓

Write the report
```

-----------------------------

# DMARC (Domain-Based Message Authentication, Reporting, and Conformance) — Simple Notes

**DMARC combines SPF and DKIM and tells the receiving mail server what to do if an email fails authentication.**

Think of it like this:

```text id="bdy6w5"
SPF  → Is the sending server authorized?

DKIM → Has the email been modified?

DMARC → What should we do if SPF and DKIM fail?
```

---

# Why Do We Need DMARC?

Suppose you receive this email:

```text id="c1ly1q"
From: support@microsoft.com
```

The receiving server performs these checks:

```text id="v7j3l3"
SPF → Pass

DKIM → Pass
```

But there's still one question:

```text id="mbu7v4"
Should this email be delivered?

Should it be marked as spam?

Should it be rejected?
```

**DMARC answers these questions.**

---

# The Relationship Between SPF, DKIM, and DMARC

```text id="f7n2mh"
Email arrives
        ↓

SPF verification
        ↓

DKIM verification
        ↓

DMARC checks both results
        ↓

Apply the domain policy
        ↓

Deliver, quarantine, or reject
```

---

# What Is Alignment?

**Alignment means that the domains used by SPF and DKIM must match the sender's domain.**

Example:

```text id="4yv1cu"
From: microsoft.com

SPF verified: microsoft.com ✓

DKIM verified: microsoft.com ✓

DMARC alignment: Success ✓
```

---

# Alignment Failure

```text id="8lrxzz"
From: microsoft.com

SPF verified: attacker.xyz ✗

DKIM verified: attacker.xyz ✗

DMARC alignment: Failed ✗
```

---

# DMARC Workflow

```text id="lygwyy"
Sender
       ↓

Email Server
       ↓

Recipient's Mail Server
       ↓

SPF Check
       ↓

DKIM Check
       ↓

DMARC Policy
       ↓

Deliver
       ↓

Spam Folder
       ↓

Reject
```

---

# DMARC Record

Example:

```text id="3l5m40"
v=DMARC1; p=quarantine; rua=mailto:postmaster@website.com
```

Let's break it down.

---

# v=DMARC1

```text id="5zh4px"
v=DMARC1
```

Meaning:

```text id="3e0srj"
This is a DMARC record.
```

---

# p=quarantine

```text id="r9xjdn"
p=quarantine
```

Meaning:

```text id="4jhlil"
Move suspicious emails to the spam folder.
```

---

# rua=mailto

```text id="4ehfx3"
rua=mailto:postmaster@website.com
```

Meaning:

```text id="b7z6lw"
Send DMARC reports to this email address.
```

The domain owner receives reports about failed email authentication.

---

# DMARC Policies

There are three DMARC policies.

---

## p=none

```text id="q71s1l"
p=none
```

Meaning:

```text id="yyoggh"
Take no action.

Only monitor the emails.
```

---

## p=quarantine

```text id="31w0d8"
p=quarantine
```

Meaning:

```text id="u1hfd4"
Move suspicious emails to the spam folder.
```

---

## p=reject

```text id="g0tt7w"
p=reject
```

Meaning:

```text id="n32ehg"
Completely reject the email.
```

---

# Example

Suppose an attacker sends this email:

```text id="mu9d2r"
From: microsoft.com
```

Authentication results:

```text id="k1bhj9"
SPF = Fail

DKIM = Fail

DMARC = p=reject
```

Result:

```text id="8mgg3g"
Reject the email.
```

The email never reaches the inbox.

---

# Microsoft Example (From Your Screenshot)

Microsoft's DMARC record:

```text id="ztd7ns"
v=DMARC1;

p=reject;

pct=100;

rua=mailto:...

ruf=mailto:...
```

---

## p=reject

```text id="1g4k3s"
p=reject
```

Meaning:

```text id="cxkkcx"
Reject all emails that fail DMARC.
```

---

## pct=100

```text id="h9t9mx"
pct=100
```

Meaning:

```text id="n4j8w9"
Apply the policy to 100% of emails.
```

---

## rua

```text id="a08kkn"
rua=mailto:...
```

Meaning:

```text id="l53vxa"
Send aggregate reports.
```

---

## ruf

```text id="l5xxbs"
ruf=mailto:...
```

Meaning:

```text id="e9p23l"
Send forensic reports.
```

---

# DMARC Domain Checker

## Tool

[DMARC Inspector by dmarcian](https://dmarcian.com/dmarc-inspector/?utm_source=chatgpt.com)

---

# How to Use It

### Step 1

Copy a domain.

Example:

```text id="k7wwz0"
microsoft.com
```

---

### Step 2

Open the DMARC Inspector.

---

### Step 3

Paste the domain.

---

### Step 4

Click **Inspect**.

---

### Step 5

Review the results.

Check:

```text id="f9fzky"
DMARC

SPF

DKIM

Policy
```

---

# SPF vs DKIM vs DMARC

| Technology | Purpose                    | Example                         |
| ---------- | -------------------------- | ------------------------------- |
| SPF        | Verify the sending server  | Is this server allowed?         |
| DKIM       | Verify the email signature | Was the email modified?         |
| DMARC      | Apply a policy             | Deliver, quarantine, or reject? |

---

# The Complete Email Authentication Process

```text id="1y29p0"
Email arrives
        ↓

SPF
        ↓

DKIM
        ↓

DMARC
        ↓

Pass
        ↓

Inbox
```

or

```text id="40jql6"
Email arrives
        ↓

SPF = Fail
        ↓

DKIM = Fail
        ↓

DMARC = Reject
        ↓

Rejected
```

---

# Real SOC Workflow

```text id="4qk8uh"
Suspicious email
        ↓

Open the header
        ↓

Check SPF
        ↓

Check DKIM
        ↓

Check DMARC
        ↓

Analyze URLs
        ↓

Analyze attachments
        ↓

Collect IOCs
        ↓

Write the report
```

-----------------

# S/MIME (Secure/Multipurpose Internet Mail Extensions) — Simple Notes

**S/MIME is a standard used to send secure emails.**

It provides two important security features:

```text id="xryikg"
1. Digital Signature

2. Encryption
```

---

# What Problem Does S/MIME Solve?

Suppose Bob sends an email to Mary.

Mary wants to know:

```text id="mqk4sq"
Did Bob really send this email?

Was the email modified?

Can anyone else read it?
```

S/MIME answers all three questions.

---

# Digital Signature

A **digital signature** proves that the sender is genuine.

---

## How It Works

```text id="8y7snn"
Bob writes an email
        ↓
Bob signs it with his PRIVATE key
        ↓
The email is sent
        ↓
Mary receives it
        ↓
Mary verifies it with Bob's PUBLIC key
        ↓
Signature verified ✓
```

---

# Security Features of a Digital Signature

---

## 1. Authentication

**Question:**

```text id="ntblj7"
Who sent the email?
```

**Answer:**

```text id="x0f7w3"
The digital certificate confirms the sender's identity.
```

---

## 2. Non-Repudiation

**Question:**

```text id="kzqrrj"
Can Bob deny sending the email?
```

**Answer:**

```text id="9owh4z"
No.
```

The digital signature proves that Bob sent the message.

---

## 3. Data Integrity

**Question:**

```text id="wpn23m"
Was the message modified?
```

**Answer:**

```text id="i7k6xh"
The signature verification will fail if the message changes.
```

---

# Encryption

**Encryption protects the email content.**

Only the intended recipient can read it.

---

## How It Works

```text id="wjjl8m"
Bob writes an email
        ↓
Bob encrypts it with Mary's PUBLIC key
        ↓
The email is sent
        ↓
Mary receives it
        ↓
Mary decrypts it with her PRIVATE key
        ↓
The message becomes readable
```

---

# Security Feature of Encryption

---

## Confidentiality

**Question:**

```text id="hiylgg"
Can other people read the email?
```

**Answer:**

```text id="uw7emg"
No.

Only Mary can read it.
```

---

# Public Key vs Private Key

| Key         | Purpose                      |
| ----------- | ---------------------------- |
| Private Key | Secret key owned by the user |
| Public Key  | Shared openly                |

---

# Bob and Mary's Example

---

## Step 1: Bob creates a certificate

```text id="81kz20"
Bob
        ↓
Creates a digital certificate
```

---

## Step 2: Bob signs the email

```text id="t9sy07"
Email
        ↓
Bob's private key
        ↓
Digital signature added
```

---

## Step 3: Bob shares his public key

```text id="9ot07s"
Bob
        ↓
Shares his public key
        ↓
Mary receives it
```

---

## Step 4: Bob gets Mary's public key

```text id="kvl7ju"
Mary
        ↓
Shares her public key
        ↓
Bob receives it
```

---

## Step 5: Bob encrypts the email

```text id="tk09ch"
Email
        ↓
Mary's public key
        ↓
Encrypted email
```

---

## Step 6: Mary verifies the signature

```text id="4hyk0l"
Encrypted email
        ↓
Bob's public key
        ↓
Signature verified
```

---

## Step 7: Mary decrypts the email

```text id="xj6gxv"
Encrypted email
        ↓
Mary's private key
        ↓
Original email
```

---

# Complete S/MIME Workflow

```text id="eyjkq6"
Bob writes an email
            ↓
Bob signs it with his private key
            ↓
Bob encrypts it with Mary's public key
            ↓
The email is sent
            ↓
Mary verifies Bob's signature
            ↓
Mary decrypts the email
            ↓
Mary reads the message
```

---

# Which Key Is Used?

| Task                       | Key                     |
| -------------------------- | ----------------------- |
| Create a digital signature | Sender's private key    |
| Verify a digital signature | Sender's public key     |
| Encrypt a message          | Recipient's public key  |
| Decrypt a message          | Recipient's private key |

---

# S/MIME vs SPF vs DKIM vs DMARC

| Technology | Purpose                                   |
| ---------- | ----------------------------------------- |
| SPF        | Verify the sending server                 |
| DKIM       | Verify the email signature                |
| DMARC      | Decide what to do if authentication fails |
| S/MIME     | Sign and encrypt the entire email         |

---

# Real SOC Investigation

If you see this in an email header:

```text id="7t6o87"
Content-Type:

application/pkcs7-mime
```

or

```text id="clif98"
Content-Type:

multipart/signed
```

the email may be using **S/MIME**.

------------------

# Email Security Defenses 

Until now, you learned how to analyze emails using:

```text
SPF
DKIM
DMARC
SMTP headers
URLs
Attachments
```

But modern organizations try to **stop phishing emails before users even see them.**

There are two types of defenses:

```text
Technical Defenses
        ↓
Automated protection

User-Facing Defenses
        ↓
Human protection
```

---

# 1. Email Filtering

**Purpose:**

Block suspicious emails automatically.

---

## How It Works

```text
Attacker sends an email
            ↓

Mail server receives the email
            ↓

Email filter checks the sender
            ↓

IP reputation check
            ↓

Domain reputation check
            ↓

Safe? → Deliver

Suspicious? → Spam folder
```

---

## What Does the Filter Check?

```text
Sender IP

Sender domain

SPF

DKIM

DMARC

Keywords

Attachments

URLs
```

---

## Example

```text
Email:

support@microsoft-login.xyz
```

The email filter checks:

```text
IP reputation → Poor

Domain reputation → Poor
```

Result:

```text
Move the email to the spam folder.
```

---

# Tool Used

## Spamhaus

**Purpose:**

Check the reputation of an IP address or domain.

---

## How to Use It

### Step 1

Copy the domain.

Example:

```text
microsoft-login.xyz
```

---

### Step 2

Open:

[Spamhaus IP and Domain Reputation Guide](https://www.spamhaus.org/resource-hub/ip-domain-reputation/?utm_source=chatgpt.com)

---

### Step 3

Search for the IP or domain.

---

### Step 4

Check the results.

---

# 2. Secure Email Gateways (SEGs)

**Purpose:**

Inspect emails before they reach the user's inbox.

---

## How It Works

```text
Attacker
      ↓

Internet
      ↓

Secure Email Gateway
      ↓

Email inspection
      ↓

Inbox
```

---

## What Does an SEG Detect?

```text
Spoofing

Impersonation

Malicious attachments

Phishing links

Spam
```

---

## Example

An attacker sends:

```text
ceo@company-support.com
```

The SEG compares it with:

```text
ceo@company.com
```

The gateway notices the mismatch.

Result:

```text
Block the email.
```

---

## Tool Example

[Cloudflare Secure Email Gateway Overview](https://www.cloudflare.com/learning/email-security/secure-email-gateway-seg/?utm_source=chatgpt.com)

---

# 3. Link Rewriting

**Purpose:**

Replace suspicious links with safe links.

---

## Original Email

```text
Click here:

http://malicious-site.com
```

---

## After Link Rewriting

```text
Click here:

https://security.company.com/check?url=malicious-site.com
```

---

## How It Works

```text
Email arrives
        ↓

The system finds a URL
        ↓

The URL is rewritten
        ↓

The user clicks the link
        ↓

The security service analyzes the website
        ↓

Safe? → Open the website

Malicious? → Block the website
```

---

## Tool Example

[Microsoft Safe Links Documentation](https://learn.microsoft.com/en-us/defender-office-365/safe-links-about?utm_source=chatgpt.com)

---

# 4. Sandboxing

**Purpose:**

Execute suspicious files in an isolated environment.

---

## How It Works

```text
Email attachment
           ↓

Sandbox
           ↓

Execute the file
           ↓

Observe its behavior
           ↓

Safe? → Deliver

Malicious? → Block
```

---

## Example

Attachment:

```text
invoice.pdf.exe
```

The sandbox discovers:

```text
Creates new files

Connects to a malicious server

Downloads malware
```

Result:

```text
Block the attachment.
```

---

## Tool Example

[Microsoft Safe Attachments Documentation](https://learn.microsoft.com/en-us/defender-office-365/safe-attachments-about?utm_source=chatgpt.com)

---

# 5. Trust and Warning Indicators

**Purpose:**

Warn users about suspicious emails.

---

## Examples

### External Sender Warning

```text
⚠ External Sender
```

---

### Suspicious Link Warning

```text
⚠ This link may be unsafe.
```

---

### Untrusted Sender Warning

```text
⚠ This message might be dangerous.
```

---

## Example

```text
From: hr@company-payroll.xyz

⚠ Warning: External Sender
```

The warning tells the user to be careful.

---

# 6. Phishing Reporting

**Purpose:**

Allow users to report suspicious emails.

---

## Example

Most email systems provide a **Report Phishing** button.

```text
Open the email
        ↓

Report Phishing
        ↓

SOC team receives the report
        ↓

The email is investigated
```

---

# Example in Gmail

```text
Open the email
        ↓

Three dots (⋮)
        ↓

Report phishing
```

---

# 7. User Awareness Training

**Purpose:**

Teach users how to recognize phishing attacks.

---

## Topics Covered

```text
Fake websites

Social engineering

Malicious attachments

Spoofed emails

Suspicious links
```

---

## Example

A user receives:

```text
Your bank account has been locked.

Click here immediately.
```

A trained user asks:

```text
Did I expect this email?

Is the sender legitimate?

Does the URL look suspicious?
```

---

# 8. Phishing Simulation Exercises

**Purpose:**

Test employees with fake phishing emails.

---

## Example

The security team sends:

```text
Congratulations!

You won a gift card.

Click here.
```

---

## Results

```text
Who clicked the link?

Who reported the email?

Who ignored it?
```

---

# Complete Email Security Workflow

```text
Attacker sends an email
             ↓

Email Filtering
             ↓

Secure Email Gateway
             ↓

SPF
             ↓

DKIM
             ↓

DMARC
             ↓

Link Rewriting
             ↓

Sandboxing
             ↓

Inbox
             ↓

User Training
             ↓

Phishing Reporting
```

---

# SOC Workflow

```text
User reports a suspicious email
                  ↓

SOC analyst investigates the email
                  ↓

Analyze headers
                  ↓

Analyze URLs
                  ↓

Analyze attachments
                  ↓

Collect IOCs
                  ↓

Write the report
```

---

# Tools Summary

| Tool             | Purpose                  |
| ---------------- | ------------------------ |
| Spamhaus         | IP and domain reputation |
| SEG              | Email inspection         |
| Safe Links       | URL protection           |
| Safe Attachments | Attachment protection    |
| Sandboxing       | Malware analysis         |

---------------------

