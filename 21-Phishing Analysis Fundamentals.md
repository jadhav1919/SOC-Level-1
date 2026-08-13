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
## Email Protocols (with simple meanings in brackets)

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

* Laptop → Email available ✅
* Mobile phone → Email not available ❌

---

# IMAP (Synchronizes emails across multiple devices)

* Emails remain stored on the mail server (**the computer that manages email accounts**)
* Emails can be accessed from multiple devices
* Sent messages are stored on the server
* Changes are synchronized (**kept the same**) across all devices
* Emails remain on the server unless they are deleted manually

### Example

You open an email on your phone.

* Phone → Email available ✅
* Laptop → Email available ✅
* Tablet → Email available ✅

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
