# Anatomy of an Email Address

Email was introduced on **ARPANET (Advanced Research Projects Agency Network - the early version of the Internet)** in the **1970s** by **Ray Tomlinson**.

He introduced the **`@` symbol**, which separates the user from the destination mail server.

---

# Structure of an Email Address

Example:

```text
david@tryhackme.com
```

An email address has **3 parts**:

```text
david      @      tryhackme.com
  │         │            │
Username   @ Symbol    Domain Name
```

---

## 1. Username

**Username (the mailbox or account name of a specific user)** identifies the recipient.

Example:

```text
david@tryhackme.com
```

`david` = Username

The username tells the mail server **which person's mailbox should receive the email**.

Examples:

```text
john@gmail.com

alice@yahoo.com

admin@company.com
```

Usernames:

```text
john
alice
admin
```

---

## 2. @ Symbol

The **`@` symbol** separates the username from the domain.

```text
david @ tryhackme.com
      ↑
```

Its job is to tell the system:

```text
Send this message to the mail server responsible for this domain.
```

Without the `@` symbol, the system cannot determine where the email should be delivered.

---

## 3. Domain Name

**Domain name (the mail server or organization responsible for receiving the email)** tells the system where the email should be delivered.

Example:

```text
david@tryhackme.com
      ↑
```

`tryhackme.com` = Domain

Examples:

```text
john@gmail.com

alice@yahoo.com

support@microsoft.com
```

Domains:

```text
gmail.com
yahoo.com
microsoft.com
```

---

# Real-Life Example

```text
security@tryhackme.com
```

| Part     | Value         | Meaning             |
| -------- | ------------- | ------------------- |
| Username | security      | Recipient's mailbox |
| @        | @             | Separator           |
| Domain   | tryhackme.com | Mail server         |

---

# Home Address Analogy

An email address is similar to a home address.

```text
Home Address

221B Baker Street
        ↓
Street = Domain

John Smith
        ↓
Person = Username
```
---------------------
## Email Protocols

When you send an email, several protocols (**rules that allow computers to communicate**) work together behind the scenes to deliver your message from the sender to the recipient. Each protocol has a different role.

* **Simple Mail Transfer Protocol (SMTP)** (**used to send emails**)
* **Post Office Protocol version 3 (POP3)** (**used to download emails to one device**)
* **Internet Message Access Protocol (IMAP)** (**used to synchronize emails across multiple devices**)

When receiving emails, the email service uses either **POP3** or **IMAP**, depending on how the mailbox is configured.

---

# POP3 (Downloads emails to one device)

* Emails are downloaded and stored on a single device (**for example, only on your laptop**)
* Sent messages are stored on the same device from which the email was sent
* Emails can usually be accessed only from that single device
* Emails are generally removed from the server (**the computer that stores emails**) after being downloaded

### Example

You download an email to your laptop.

* Laptop → Email available 
* Mobile phone → Email not available 

---

# IMAP (Synchronizes emails across multiple devices)

* Emails remain stored on the mail server (**the computer that manages email accounts**)
* Emails can be accessed from multiple devices
* Sent messages are stored on the server
* Changes are synchronized (**kept the same**) across all devices
* Emails remain on the server unless they are deleted manually

### Example

You open an email on your phone.

* Phone → Email available 
* Laptop → Email available 
* Tablet → Email available 

Everything stays synchronized.

---

# Comparison: POP3 vs IMAP

| POP3                                       | IMAP                        |
| ------------------------------------------ | --------------------------- |
| Downloads emails                           | Synchronizes emails         |
| Single device                              | Multiple devices            |
| Emails are usually removed from the server | Emails remain on the server |
| No synchronization                         | Full synchronization        |

---

# An Email's Journey (Step by Step)

## Step 1: User sends an email

The sender's email application (**such as Gmail or Outlook**) sends the email to the sender's mail server using **SMTP**.

```
User → SMTP → Mail Server
```

---

## Step 2: The mail server queries DNS

The sending server asks **DNS (Domain Name System - the Internet's phone book)** for the recipient's mail server address.

```
Mail Server → DNS
```

---

## Step 3: DNS responds

DNS returns the address of the recipient's mail server.

```
DNS → Recipient's Mail Server Address
```

---

## Step 4: Email is delivered

The email travels across the Internet and reaches the recipient's mail server.

```
Sender's Mail Server → Internet → Recipient's Mail Server
```

---

## Step 5: The recipient checks the mailbox

The recipient opens an email application such as Gmail or Outlook.

```
Recipient → Email Client
```

---

## Step 6: Email is retrieved

The email client connects to the mail server.

* **POP3** → Downloads the email.
* **IMAP** → Synchronizes the email.

```
POP3 → Download

OR

IMAP → Synchronize
```

---

# Complete Flow

```text
Sender
   │
   ▼
Email Client
   │
   ▼
SMTP (Sends email)
   │
   ▼
Sender's Mail Server
   │
   ▼
DNS (Finds the recipient's mail server)
   │
   ▼
Recipient's Mail Server
   │
   ▼
POP3 (Download) or IMAP (Synchronize)
   │
   ▼
Recipient's Device
```

---

# Easy way to remember

| Protocol | Remember it as         |
| -------- | ---------------------- |
| SMTP     | Send                   |
| POP3     | Pull to one device     |
| IMAP     | Synchronize everywhere |

**Memory trick:**

* **SMTP = Send Mail**
* **POP3 = One PC**
* **IMAP = Many Apps**
-----------------------------

# Email Structure

When an email arrives in your inbox, it has **two main parts**:

```text
Email
│
├── Header (information about the email)
│
└── Body (the actual message)
```

---

# 1. Email Header

The **header** contains **metadata** (information about the email).

Think of it like the information written on an envelope before you open a letter.

An email header tells you:

* Who sent the email
* Who received the email
* When it was sent
* Which servers handled the email
* Which IP address was used to send the email

This information is very important when investigating **phishing emails** and **malicious emails**.

---

## Important Header Fields

| Header Field | Meaning                            |
| ------------ | ---------------------------------- |
| From         | Sender's email address             |
| To           | Recipient's email address          |
| Reply-To     | Address where replies will be sent |
| Subject      | Title of the email                 |
| Date         | Date and time the email was sent   |

---

## Example

```text
From: hacker@example.com
To: employee@company.com
Reply-To: fake@evil.com
Subject: Urgent! Update Your Password
Date: Thu, 13 Aug 2026 10:30:15
```

### What can we learn?

```text
From → hacker@example.com
```

The sender claims to be **[hacker@example.com](mailto:hacker@example.com)**.

---

```text
To → employee@company.com
```

The message was sent to **[employee@company.com](mailto:employee@company.com)**.

---

```text
Reply-To → fake@evil.com
```

If the recipient clicks **Reply**, the response will go to **[fake@evil.com](mailto:fake@evil.com)**.

 In phishing emails, the **From** address and the **Reply-To** address are often different.

---

```text
Subject → Urgent! Update Your Password
```

The attacker is trying to create a sense of urgency.

---

```text
Date → Thu, 13 Aug 2026 10:30:15
```

This shows when the email was sent.

---

# 2. Email Body

The **body** contains the actual message.

Example:

```text
Dear Employee,

Your password will expire today.

Click the link below to update it.

http://fake-website.com

Thank you,
IT Department
```

The body may contain:

* Plain text
* HTML
* Images
* Links
* Attachments

---

# Viewing the Message Source in Thunderbird

The normal email view does **not** show all the technical information.

To see everything:

### Method 1

```text
View
 ↓
Message Source
```

---

### Method 2

Press:

```text
Ctrl + U
```

---

# What Is Message Source?

The **message source** displays the complete email.

```text
Message Source
│
├── Full email headers
├── Email body
├── HTML code
├── Mail servers
└── Originating IP address
```

---

# Why Is This Important for Security Analysts?

When analyzing a suspicious email, the message source can help you identify:

* The sender
* The originating IP address
* Fake email addresses
* Spoofed domains
* Malicious links
* Suspicious attachments

--------------------

# Email Body 

The **email body** contains the actual message that the sender wants to deliver.

An email body can be written in **two formats**:

1. Plain Text
2. HTML

---

# 1. Plain Text Email

A plain text email contains only text.

Example:

```text
Hello,

Your meeting is scheduled for tomorrow at 10:00 AM.

Thank you.
```

**Characteristics:**

* No images
* No colors
* No buttons
* No special formatting

---

# 2. HTML Email

An HTML email uses **HTML (HyperText Markup Language)** to format the message.

HTML allows an email to contain:

* Images
* Hyperlinks
* Buttons
* Different fonts
* Colors
* Tables

---

## Example

### Rendered Email (What the user sees)

```text
------------------------------------------------
Company Logo

Dear Customer,

Your account requires verification.

[Verify Account]

Thank you,
Support Team
------------------------------------------------
```

---

### HTML Source (What the analyst sees)

```html
<html>
<body>

<img src="company_logo.png">

<p>Dear Customer,</p>

<p>Your account requires verification.</p>

<a href="http://fake-website.com">
Verify Account
</a>

</body>
</html>
```

---

# Why Do Security Analysts Inspect HTML?

Attackers often hide malicious content inside HTML.

The normal email view may hide important details.

By inspecting the HTML source, an analyst can find:

* Hidden links
* Malicious URLs
* Tracking images
* Fake buttons
* Suspicious scripts

---

# Viewing the HTML Source

In Thunderbird:

```text
View
 ↓
Message Source
```

Or press:

```text
Ctrl + U
```

---

# Example of a Phishing Attack

## What the victim sees

```text
Click here to update your Microsoft password.
```

---

## What the analyst sees

```html
<a href="http://evil-site.com">
Update your Microsoft password
</a>
```

---

⚠️ The text says **Microsoft**, but the actual link points to **evil-site.com**.

This is why analysts inspect the HTML source.

---

# Email Attachments

Emails can also contain attachments.

Examples:

* PDF files
* Word documents
* Images
* ZIP files

---

# Example

```text
Email
│
├── Header
│
├── Body
│
└── Attachment
        │
        ▼
    report.pdf
```

---

# How Attachments Are Stored

Attachments are embedded inside the email source.

When you inspect the source code, you'll see headers such as:

```text
Content-Type: application/pdf
Content-Disposition: attachment;
filename="report.pdf"

Content-Transfer-Encoding: base64
```

---

# Important Attachment Headers

| Header                    | Meaning                                     |
| ------------------------- | ------------------------------------------- |
| Content-Type              | File type                                   |
| Content-Disposition       | Indicates whether the file is an attachment |
| Filename                  | Name of the file                            |
| Content-Transfer-Encoding | Encoding method                             |

---

## Content-Type

```text
Content-Type: application/pdf
```

Meaning:

```text
application/pdf
        │
        ▼
The attachment is a PDF file.
```

---

## Content-Disposition

```text
Content-Disposition: attachment;
filename="report.pdf"
```

Meaning:

```text
The file is an attachment.

File name = report.pdf
```

---

## Content-Transfer-Encoding

```text
Content-Transfer-Encoding: base64
```

Meaning:

The file has been converted into **Base64 text**.

---

# What Is Base64?

Base64 converts binary data into text.

Example:

```text
Original File
       │
       ▼
PDF File
       │
       ▼
Base64 Encoding
       │
       ▼
JVBERi0xLjQKJcfs...
```

The long text after the header is the encoded file.

---

# Reconstructing an Attachment

```text
Base64 Data
       │
       ▼
Decode Base64
       │
       ▼
Original PDF File
```

Security analysts can decode the Base64 data to recover the original attachment.

Tools used:

* CyberChef
* Base64 decoders
* Forensic tools

---

# Why Is This Important in Email Analysis?

Attackers often hide malware inside attachments.

Examples:

| Attachment    | Possible Threat |
| ------------- | --------------- |
| invoice.pdf   | Malicious PDF   |
| document.docx | Macro malware   |
| update.zip    | Trojan          |
| report.exe    | Malware         |

By reconstructing and analyzing attachments, analysts can:

* Recover hidden files
* Identify malware
* Extract indicators of compromise (IOCs)
* Determine whether an email is malicious

---------------------

# Malicious Emails 

Attackers frequently use **email** as their first method of attack.

Instead of attacking a computer directly, they often **trick people into helping them**.

This technique is called **social engineering**.

---

# What Is Social Engineering?

**Social engineering** is the manipulation of people into revealing information or performing actions.

Examples:

* Clicking a malicious link
* Opening an infected attachment
* Entering a password on a fake website
* Sending confidential information

---

# Types of Malicious Emails

---

## 1. Spam

**Spam = Unwanted bulk emails**

These emails are sent to thousands or millions of users.

Examples:

* Advertisements
* Promotions
* Fake lottery messages

---

## Malspam

**Malspam = Malicious spam**

The email contains:

* Malware
* Malicious attachments
* Phishing links

---

## 2. Phishing

**Phishing** is an attack in which an attacker pretends to be a trusted person or company.

Goal:

* Steal passwords
* Steal financial information
* Install malware

---

### Example

```text
From: support@microsof.com

Subject: Your account will be locked.

Click here to verify your account.
```

The attacker is pretending to be Microsoft.

---

## 3. Spear Phishing

**Spear phishing** is a **targeted phishing attack**.

Instead of attacking everyone, the attacker targets a specific person.

---

### Example

```text
Hello Sai,

Your IIIT Kottayam email account requires verification.

Please sign in immediately.
```

The attacker uses your:

* Name
* College
* Email address

This makes the email appear legitimate.

---

## 4. Whaling

**Whaling** is a phishing attack that targets **high-level executives**.

Targets:

* CEO
* CFO
* Directors
* Managers

---

### Example

```text
Dear CFO,

Please approve the attached payment immediately.
```

The goal is usually to steal money or sensitive company data.

---

## 5. Smishing

**Smishing = SMS + Phishing**

The attack is performed through text messages.

---

### Example

```text
Your bank account has been suspended.

Click this link immediately.
```

---

## 6. Vishing

**Vishing = Voice + Phishing**

The attack is performed through phone calls.

---

### Example

```text
Hello.

This is your bank's security department.

Please verify your account number.
```

---

# Anatomy of a Phishing Email

Most phishing emails follow the same pattern.

---

## 1. Spoofed Sender Address

The sender's address looks legitimate.

---

### Legitimate Email

```text
noreply@microsoft.com
```

---

### Fake Email

```text
noreply@microsof.com
```

Notice:

```text
microsoft.com
         ↓

microsof.com
```

The letter **"t"** is missing.

---

## 2. Urgent Subject Line

Attackers create panic.

---

### Examples

```text
Your account will be locked in 24 hours.
```

```text
Immediate action required.
```

```text
Your password has expired.
```

People often make mistakes when they feel pressured.

---

## 3. Brand Impersonation

The email copies the appearance of a real company.

Attackers imitate:

* Logos
* Colors
* Fonts
* Layouts

---

### Example

A fake Microsoft email may contain:

```text
✓ Microsoft logo

✓ Microsoft colors

✓ Microsoft formatting
```

But the link leads to a malicious website.

---

## 4. Grammar and Spelling Mistakes

Older phishing emails often contained:

```text
Dear Customer,

Your acount has ben suspend.

Pleaze login now.
```

Common signs:

* Incorrect grammar
* Misspelled words
* Strange sentence structure

⚠️ **Important:** AI-generated phishing emails often have perfect grammar.

---

## 5. Generic Greetings

Instead of using your real name, attackers use generic greetings.

---

### Examples

```text
Dear Customer
```

```text
Dear User
```

```text
Dear Employee
```

---

### Legitimate Organizations Usually Use

```text
Hello Sai
```

```text
Dear Jadhav Sai
```

---

## 6. Hidden or Shortened Links

The visible text and the actual destination may be different.

---

### What the User Sees

```text
Sign in to Microsoft
```

---

### Actual Link

```text
http://malicious-site.com
```

---

### Shortened Links

```text
bit.ly/secure-login
```

Shortened URLs hide the real destination.

---

## 7. Malicious Attachments

Attackers often disguise malware as legitimate files.

---

### Examples

```text
invoice.pdf.exe
```

```text
salary.xlsx.exe
```

```text
payment.zip
```

⚠️ Windows may hide the final extension.

The victim sees:

```text
invoice.pdf
```

But the actual file is:

```text
invoice.pdf.exe
```

---

# Safe Analysis

Never click suspicious links directly.

---

## Defanging

**Defanging** makes dangerous links unclickable.

---

### Original URL

```text
http://www.suspiciousdomain.com
```

---

### Defanged URL

```text
hxxp[://]www[.]suspiciousdomain[.]com
```

---

## Defanging an Email Address

### Original

```text
admin@suspiciousdomain.com
```

---

### Defanged

```text
admin[@]suspiciousdomain[.]com
```

---

## Defanging an IP Address

### Original

```text
192.168.1.100
```

---

### Defanged

```text
192[.]168[.]1[.]100
```

---

# Phishing Investigation Workflow

```text
Suspicious Email
        │
        ▼
Check the Sender
        │
        ▼
Check the Subject
        │
        ▼
Inspect the Email Body
        │
        ▼
Analyze Links
        │
        ▼
Analyze Attachments
        │
        ▼
Defang URLs and IP Addresses
        │
        ▼
Determine Whether the Email Is Malicious
```

-------------------
