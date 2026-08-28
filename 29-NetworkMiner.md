# NetworkMiner 

## 1. First, what problem are we trying to solve?

Imagine a company has a network:

```text
                Internet
                   |
             [Router/Firewall]
                   |
        -------------------------
        |           |           |
      PC-1        PC-2       Server
   192.168.1.10  .11         .20
```

All these machines communicate by sending **network traffic**.

For example:

```text
PC-1  ────────>  Server
       HTTP

PC-2  ────────>  Google
       DNS

PC-1  ────────>  Server
       FTP
```

If something suspicious happens, a security analyst may want to know:

* Which computers were communicating?
* What IP addresses were involved?
* What operating systems were used?
* What protocols were used?
* Were files transferred?
* Were credentials exposed?
* Was someone scanning ports?
* Was an attacker using a tool such as Nmap?
* What hosts were present in the capture?

This is where **network forensics** comes in.

---

# 2. What is Network Forensics?

**Network forensics** means investigating network traffic to discover what happened on a network.

Think of it like a crime investigation.

### Normal investigation

```text
Crime scene
    ↓
Collect evidence
    ↓
Analyze evidence
    ↓
Find what happened
```

### Network forensics

```text
Network traffic
      ↓
Collect traffic
      ↓
Analyze packets
      ↓
Identify hosts / protocols / files / credentials
      ↓
Understand what happened
```

The evidence is the **network traffic**.

---

# 3. So what is NetworkMiner?

**NetworkMiner is a Network Forensic Analysis Tool (NFAT).**

Its main purpose is to take network traffic and quickly extract useful information from it.

Instead of forcing you to manually inspect every packet, NetworkMiner tries to organize the information for you.

For example, suppose you have a PCAP containing:

```text
100,000 packets
```

Looking at every packet manually would be painful.

NetworkMiner can give you a quick overview such as:

```text
Hosts
├── 192.168.1.10
│   ├── Windows
│   ├── MAC address
│   └── Hostname
│
├── 192.168.1.20
│   ├── Linux
│   └── HTTP server
│
└── 192.168.1.30
    └── FTP activity

Files
├── image.jpg
├── index.html
└── document.pdf

Credentials
├── username
└── password

Protocols
├── HTTP
├── DNS
├── FTP
└── SMB
```

That is why NetworkMiner is useful.

---

# 4. What does "Network Forensic Analysis Tool" mean?

Let's break the name apart.

### Network

It works with **network traffic**.

### Forensic

It is used to investigate evidence and determine **what happened**.

### Analysis

It examines the captured traffic and extracts useful information.

### Tool

It is software that helps an analyst perform this investigation.

So:

```text
Network + Forensic + Analysis + Tool
                 ↓
             NetworkMiner
```

---

# 5. The most important idea: NetworkMiner is mainly an overview tool

This is probably the **most important thing to understand** from this section.

NetworkMiner is excellent for answering:

> **"What is inside this network traffic?"**

For example:

```text
PCAP
 ↓
NetworkMiner
 ↓
Quick overview
 ↓
Hosts
Protocols
OS
Files
Credentials
Keywords
Possible anomalies
```

Then, if you find something interesting, you can investigate it deeply using **Wireshark**.

Think of it like this:

```text
                 PCAP
                  |
                  ↓
            NetworkMiner
                  |
          Quick overview
                  |
       ┌──────────┼──────────┐
       ↓          ↓          ↓
     Hosts      Files      Credentials
       |
       ↓
Interesting activity?
       |
       ↓
     Wireshark
       |
       ↓
Deep investigation
```

### Simple analogy

Imagine you have a huge book.

**NetworkMiner** is like a tool that quickly tells you:

```text
Who are the characters?
What chapters are important?
What objects appear?
What suspicious things are present?
```

**Wireshark** is like reading the actual pages word by word.

So:

> **NetworkMiner = quick overview**
>
> **Wireshark = deep packet investigation**

---

# 6. What is a PCAP?

You will see **PCAP** everywhere in network forensics.

PCAP means a **packet capture file**.

It is basically a file containing captured network traffic.

Imagine this:

```text
Network
   ↓
Packets
   ↓
Captured
   ↓
Saved into a file
   ↓
capture.pcap
```

That PCAP can later be opened by tools such as:

* NetworkMiner
* Wireshark
* tcpdump

For example:

```text
investigation.pcap
       ↓
   NetworkMiner
       ↓
Extract information
```

---

# 7. Why is PCAP important?

Suppose an attack happened at 2:00 PM.

The security team captured the network traffic.

Later, the analyst receives:

```text
attack.pcap
```

The analyst can investigate the evidence **offline**.

They don't need the attack to happen again.

```text
Attack happened
      ↓
Traffic captured
      ↓
       PCAP
      ↓
Later investigation
      ↓
NetworkMiner / Wireshark
```

This is called **offline analysis**.

---

# 8. NetworkMiner has two important ways of working

Your notes mention two operating modes.

## Mode 1 — Sniffer Mode

NetworkMiner can capture traffic directly from a network interface.

Conceptually:

```text
Network
   ↓
Network traffic
   ↓
NetworkMiner
   ↓
Analyze traffic
```

However, this is **not its strongest purpose**.

The room specifically points out that NetworkMiner is **not intended to be your primary sniffer**.

Tools such as:

```text
Wireshark
tcpdump
```

are generally better suited for dedicated packet capture/sniffing.

So remember:

> **NetworkMiner can sniff, but its main strength is network forensics and traffic analysis.**

---

# 9. Mode 2 — PCAP Parsing

This is the more important mode for this room.

You already have:

```text
traffic.pcap
```

You give it to NetworkMiner:

```text
             traffic.pcap
                  |
                  ↓
             NetworkMiner
                  |
       ┌──────────┼──────────┐
       ↓          ↓          ↓
     Hosts      Files      Credentials
       ↓          ↓          ↓
      OS       Images      Keywords
```

NetworkMiner parses the captured traffic and extracts useful information.

This is why it is very useful during the **initial investigation**.

---

# 10. What can NetworkMiner discover?

Now let's understand the capabilities from your material.

## A. Hosts

NetworkMiner can identify hosts involved in the traffic.

For example:

```text
192.168.1.10
192.168.1.20
192.168.1.50
```

It may also provide information such as:

```text
IP address
MAC address
Hostname
OS
```

This helps answer:

> **Who is communicating?**

---

## B. OS Fingerprinting

NetworkMiner can attempt to determine the operating system of a host.

For example:

```text
192.168.1.10
      ↓
Windows
```

or:

```text
192.168.1.20
      ↓
Linux
```

This process is called:

> **OS fingerprinting**

It doesn't simply magically know the OS. It analyzes characteristics visible in network traffic.

The room mentions that NetworkMiner's OS fingerprinting relies heavily on technologies such as **Satori** and **p0f**.

For now, you only need to remember:

```text
Network traffic
      ↓
Traffic characteristics
      ↓
OS fingerprinting
      ↓
Possible OS
```

---

# 11. C. Protocol identification

NetworkMiner can identify protocols present in the captured traffic.

For example:

```text
PCAP
 ↓
NetworkMiner
 ↓
Protocols
 ├── HTTP
 ├── DNS
 ├── FTP
 ├── SMB
 └── etc.
```

This helps you understand:

> **What kind of communication is happening?**

---

# 12. D. File extraction

This is one of NetworkMiner's useful forensic features.

Suppose someone transfers an image through network traffic:

```text
Computer A
     |
     | image.jpg
     ↓
Computer B
```

If the traffic contains enough information and the protocol is supported, NetworkMiner can potentially reconstruct/extract the file.

So:

```text
PCAP
 ↓
NetworkMiner
 ↓
File extraction
 ↓
image.jpg
```

The room mentions things such as:

* Images
* HTML files
* Emails

This can be extremely useful in investigations.

For example, an analyst might discover:

```text
Files extracted:

photo.jpg
login.html
email.eml
```

Then the analyst can investigate those files separately.

---

# 13. E. Credential grabbing

This one is very important for security analysis.

If credentials are transmitted in a way that exposes them in captured traffic, NetworkMiner may be able to extract them.

For example:

```text
Username: alice
Password: password123
```

⚠️ This does **not** mean NetworkMiner can magically break encrypted passwords.

The important idea is:

> If authentication information is visible in the captured traffic, a forensic tool may be able to identify it.

This is especially relevant to **cleartext protocols**.

For example:

```text
FTP
HTTP
Telnet
```

can expose sensitive information when used without appropriate encryption.

---

# 14. F. Cleartext keyword parsing

NetworkMiner can also search traffic for cleartext strings/keywords.

Imagine traffic contains:

```text
username=admin
password=test123
```

NetworkMiner may identify useful cleartext information.

So:

```text
PCAP
 ↓
NetworkMiner
 ↓
Cleartext strings
 ↓
Interesting keywords
```

This is useful during incident response.

---

# 15. NetworkMiner in one picture

Let's connect everything we learned.

```text
                    PCAP
                     |
                     ↓
               NetworkMiner
                     |
       ┌─────────────┼──────────────┐
       ↓             ↓              ↓
     Hosts        Protocols       Files
       |             |              |
       ↓             ↓              ↓
 IP / MAC / OS    HTTP/DNS/FTP   Images/HTML
       |
       ↓
   OS Fingerprint

       ┌─────────────────────────────┐
       ↓                             ↓
 Credentials                  Cleartext Keywords
       |                             |
       ↓                             ↓
 username/password            useful strings
```

This is the core idea of NetworkMiner.

---

# 16. What is NetworkMiner good at?

The room calls these its advantages.

### NetworkMiner is good at:

| Capability                    | Why useful?                         |
| ----------------------------- | ----------------------------------- |
| **OS fingerprinting**         | Helps identify the operating system |
| **File extraction**           | Quickly recover transferred files   |
| **Credential discovery**      | Find exposed credentials            |
| **Cleartext keyword parsing** | Find useful strings                 |
| **Overall overview**          | Quickly understand a PCAP           |

The common theme is:

> **NetworkMiner helps you quickly understand a network capture.**

---

# 17. What is NetworkMiner NOT good at?

This is equally important.

### 1. Large PCAP investigation

If you have a gigantic PCAP, NetworkMiner may not be the best tool for detailed investigation.

### 2. Detailed packet investigation

It isn't designed for manually inspecting every packet.

### 3. Advanced filtering

Its filtering capabilities are more limited compared with Wireshark.

### 4. Deep payload analysis

Wireshark is much stronger for detailed packet/payload inspection.

### 5. Primary sniffing

Although NetworkMiner can sniff traffic, it isn't intended to replace dedicated sniffing tools.

---

# 18. NetworkMiner vs Wireshark

This is an important exam/interview concept.

Think:

```text
             PCAP
               |
       ┌───────┴────────┐
       ↓                ↓
 NetworkMiner       Wireshark
       ↓                ↓
 Quick overview     Deep analysis
       ↓                ↓
 Hosts              Packets
 OS                 Protocols
 Files              Payloads
 Credentials        Statistics
 Keywords           Detailed filtering
```

### Example investigation

You receive:

```text
suspicious.pcap
```

### Step 1 NetworkMiner

You first ask:

```text
Who is here?
What machines exist?
What OS are they using?
What protocols appear?
Were files transferred?
Are credentials visible?
Is anything immediately suspicious?
```

### Step 2  Wireshark

Then:

```text
Why did this communication happen?
Which exact packets are suspicious?
What was the payload?
What sequence of events occurred?
What exact protocol behavior occurred?
```

Therefore:

> **NetworkMiner first → Wireshark deeper**

This is a very useful workflow.

-------------------
Yes. This section is basically teaching you **how to read the NetworkMiner interface** and, more importantly, **which tab to look at for which type of evidence**.

We’ll learn it in a logical order instead of memorizing the menu names.

# NetworkMiner Interface — Part 2

## 1. First understand the big picture

When you open NetworkMiner, think of the interface like an **investigation dashboard**.

You give NetworkMiner a PCAP:

```text
                  PCAP
                   │
                   ▼
             ┌───────────┐
             │NetworkMiner│
             └─────┬─────┘
                   │
       ┌───────────┼────────────┐
       │           │            │
       ▼           ▼            ▼
     Hosts       Sessions       DNS
       │
       ├── Who is communicating?
       ├── What OS?
       └── What ports?
       
       ▼
   Credentials
       │
       └── Were credentials exposed?

       ▼
     Files
       │
       └── What files were transferred?

       ▼
     Images
       │
       └── What images were transferred?

       ▼
   Parameters
       │
       └── What values were transmitted?

       ▼
    Keywords
       │
       └── Is something interesting hidden in text?

       ▼
    Messages
       │
       └── What emails/chats were exchanged?

       ▼
   Anomalies
       │
       └── Is something suspicious detected?
```

So don't think of these as random tabs.

Each tab answers a **different investigation question**.

---

# 2. Landing Page

When you first open NetworkMiner, you see the **landing page**.

At this point, you haven't necessarily loaded a PCAP yet.

Think:

```text
Open NetworkMiner
       ↓
Landing Page
       ↓
Load PCAP
       ↓
Start investigation
```

The landing page is basically your starting point.

---

# 3. File Menu

The **File** menu is where you interact with PCAP files.

The most important action for this room is:

```text
File
 ↓
Open PCAP
 ↓
NetworkMiner processes PCAP
```

You can also **drag and drop** a PCAP into NetworkMiner.

### Example

Suppose you have:

```text
attack.pcap
```

You can:

```text
attack.pcap
     ↓
Drag into NetworkMiner
     ↓
NetworkMiner processes it
     ↓
Investigation tabs populate
```

---

## What is "Pcap over IP"?

NetworkMiner can also receive PCAP data over a network connection.

Conceptually:

```text
Machine A
   │
   │ PCAP data
   ▼
Network
   │
   ▼
NetworkMiner
```

But **you don't need this for this room**.

For now, remember only:

> **File menu → Load a PCAP for investigation.**

---

# 4. Tools Menu

The **Tools** menu is mainly for managing the current investigation data.

One important function is clearing the dashboard/captured data.

Think:

```text
Current investigation
        ↓
Tools
        ↓
Clear data
        ↓
Fresh investigation
```

This becomes useful when you finish investigating one PCAP and want to start another.

---

# 5. Help Menu

Very simple.

The **Help** menu provides information such as:

* NetworkMiner version
* Updates
* Application information

You don't need to spend much time here for forensic analysis.

---

# 6. Case Panel

Now we get to something more important.

The **Case Panel** keeps track of the PCAP files you're investigating.

Imagine you're investigating several captures:

```text
Case Panel
│
├── attack1.pcap
├── suspicious.pcap
└── traffic.pcap
```

It allows you to:

* View loaded PCAPs
* Reload/refresh them
* View metadata
* Remove loaded files

---

## What is metadata?

**Metadata = information about the file.**

For example:

```text
PCAP
 │
 ├── Filename
 ├── Size
 ├── Capture information
 ├── Timing information
 └── Other file details
```

It isn't necessarily the actual packet payload.

Think:

```text
Data
    = the actual content

Metadata
    = information describing the data
```

### Example

For a photo:

```text
Actual data:
[image itself]

Metadata:
Filename: photo.jpg
Size: 2 MB
Format: JPEG
```

Same basic idea applies to a PCAP.

---

# 7. HOSTS — ⭐ Very Important

Now we reach one of the most important tabs.

The **Hosts** tab answers:

> **Who is present in this network traffic?**

Suppose the PCAP contains:

```text
192.168.1.10
192.168.1.20
192.168.1.50
```

NetworkMiner can organize those hosts for you.

You may see:

```text
Host
├── IP address
├── MAC address
├── OS
├── Open ports
├── Packets sent
├── Packets received
├── Sessions
└── Other host details
```

---

# 8. IP Address

An IP address identifies a host at the network layer.

Example:

```text
192.168.1.10
```

NetworkMiner can show which hosts were observed in the PCAP.

So your first question can be:

> **Which machines are involved?**

---

# 9. MAC Address

A MAC address identifies a network interface at the data-link layer.

Example:

```text
AA:BB:CC:11:22:33
```

So NetworkMiner can potentially give you:

```text
IP:
192.168.1.10

MAC:
AA:BB:CC:11:22:33
```

This helps correlate network activity with a particular network interface.

---

# 10. OS Type

NetworkMiner can perform **OS fingerprinting**.

For example:

```text
192.168.1.10
      ↓
Windows
```

or:

```text
192.168.1.20
      ↓
Linux
```

Remember:

> OS fingerprinting means **inferring the likely operating system from characteristics visible in network traffic**.

It isn't necessarily a 100% guarantee.

---

# 11. Open Ports

NetworkMiner can also show ports associated with hosts.

For example:

```text
192.168.1.20

Ports:
22
80
443
```

You can start thinking:

```text
22  → SSH
80  → HTTP
443 → HTTPS
```

This gives you an idea of what services may be running.

---

# 12. Sent and Received Packets

NetworkMiner can show traffic information such as:

```text
Packets Sent
Packets Received
```

Imagine:

```text
Host A
Sent:     10,000 packets
Received:  2,000 packets
```

That tells you something about the host's communication behavior.

It can be useful when looking for unusual traffic patterns.

---

# 13. Sessions

A **session** represents a communication interaction between hosts.

For example:

```text
Client
192.168.1.10
     │
     │ TCP 80
     ▼
Server
192.168.1.20
```

NetworkMiner's Sessions tab can show things such as:

* Frame number
* Client address
* Server address
* Source port
* Destination port
* Protocol
* Start time

---

## Understand the difference

### Hosts

Answers:

> **Who is communicating?**

### Sessions

Answers:

> **Who communicated with whom, using what ports/protocol, and when?**

For example:

```text
HOSTS

192.168.1.10
192.168.1.20
192.168.1.30
```

Then Sessions might tell you:

```text
192.168.1.10
     │
     │ TCP/80
     ▼
192.168.1.20
```

and:

```text
192.168.1.20
     │
     │ UDP/53
     ▼
192.168.1.1
```

---

# 14. Session Filtering

The Sessions tab has a filtering/search bar.

You can search for specific information inside frames.

The room mentions four input types:

### ExactPhrase

Search for an exact phrase.

Conceptually:

```text
"login failed"
```

You want that exact phrase.

---

### AllWords

Search where **all specified words** occur.

Example:

```text
admin password
```

The result needs to contain both concepts/words according to the filter's matching behavior.

---

### AnyWord

Search for **any** of the specified words.

Example:

```text
admin password login
```

A result containing any of those terms can match.

---

### RegExe

This means **Regular Expression**.

Regular expressions allow you to create more advanced search patterns.

For example, instead of searching only for:

```text
password
```

you could create a pattern to find variations of a value.

You don't need to master regex yet. Just remember:

> **RegExe = advanced pattern-based searching.**

---

# 15. DNS — ⭐ Important

The **DNS** tab shows DNS-related activity found in the PCAP.

Remember what DNS does:

```text
www.example.com
       ↓
      DNS
       ↓
IP address
```

NetworkMiner organizes DNS queries and answers.

You may see:

```text
Client
192.168.1.10

Query:
example.com

Answer:
93.184.216.34
```

---

## DNS information

The DNS tab can provide:

* Frame number
* Timestamp
* Client/server
* Source/destination ports
* IP TTL
* DNS timing
* Transaction ID
* Query type
* DNS query
* DNS answer

The important part for beginners is:

```text
Who asked?
     ↓
What domain?
     ↓
What answer did DNS provide?
     ↓
When did it happen?
```

---

# 16. Credentials — 🚨 Very Important

The **Credentials** tab is one of the most valuable tabs during a security investigation.

It can show credentials or authentication-related information extracted from traffic.

For example:

```text
Protocol: FTP

Username:
alice

Password:
********
```

Depending on the protocol and captured traffic, NetworkMiner can identify things such as:

* Kerberos hashes
* NTLM hashes
* RDP cookies
* HTTP cookies
* HTTP requests
* IMAP information
* FTP credentials
* SMTP information
* MS SQL authentication information

---

## Important distinction: password vs hash

Don't confuse these.

### Password

```text
Password:
hello123
```

This may be directly usable if exposed in cleartext.

### Hash

```text
Password
   ↓
Hash function
   ↓
a9f...something...
```

A hash is not the original password.

Tools such as Hashcat or John the Ripper can sometimes attempt to recover the original password through password-cracking techniques, depending on the hash type and circumstances.

For now:

> **Credentials tab = look for authentication information exposed or extracted from network traffic.**

---

# 17. Files — ⭐ Very Important

The **Files** tab shows files that NetworkMiner reconstructed/extracted from the PCAP.

Imagine:

```text
Computer A
     │
     │ document.pdf
     ▼
Computer B
```

NetworkMiner may reconstruct:

```text
document.pdf
```

and show details such as:

```text
Filename
Extension
Size
Source
Destination
Source port
Destination port
Protocol
Timestamp
Path
```

This is extremely useful in incident response.

---

## Example

Suppose you find:

```text
Files

invoice.pdf
photo.jpg
malware.exe
secret.zip
```

Now you have something concrete to investigate.

You can ask:

```text
Who sent it?
       ↓
Who received it?
       ↓
When?
       ↓
Which protocol?
       ↓
What was inside?
```

---

# 18. Images

The **Images** tab is basically a convenient view of extracted image files.

Instead of seeing only:

```text
photo.jpg
```

you can actually preview the image.

You can:

* Open the image
* Zoom in/out
* View details

Hovering over an image can show information such as:

```text
Source
Destination
File path
```

So:

```text
Files
 ↓
Many file types

Images
 ↓
Specifically extracted images
```

---

# 19. Parameters

This tab can be confusing at first.

A **parameter** is basically a named value transmitted in network communication.

For example, imagine an HTTP request:

```text
/login?username=admin&role=user
```

Here:

```text
username = admin
role     = user
```

Those are parameters.

NetworkMiner can show things like:

```text
Parameter name
Parameter value
Frame number
Source host
Destination host
Source port
Destination port
Timestamp
```

So you can think:

```text
Parameter
    ↓
Name + Value

username = admin
role     = user
```

This can be useful when investigating web traffic.

---

# 20. Keywords — ⭐ Very Useful

The **Keywords** tab searches processed PCAP data for interesting words.

Imagine you're investigating a suspicious capture.

You might be interested in:

```text
password
admin
secret
login
malware
flag
```

You add those keywords.

Then NetworkMiner searches the processed PCAP for them.

You might get:

```text
Keyword: password

Frame: 1234
Context: username=admin&password=test123
Source: 192.168.1.10
Destination: 192.168.1.20
```

Now you have a clue.

---

## Important detail

After changing the keyword list:

```text
Add keywords
      ↓
Reload case files
      ↓
NetworkMiner searches again
```

If you forget to reload, you may not see the expected results.

### Remember:

> **Keywords → add search terms → reload case files → investigate matches.**

---

# 21. Messages

The **Messages** tab extracts communication such as:

* Emails
* Chats
* Messages

You might see:

```text
From:
alice@example.com

To:
bob@example.com

Time:
10:32

Protocol:
SMTP

Size:
25 KB
```

You can then investigate the message further.

If there is an attachment:

```text
Email
  │
  ├── Message
  │
  └── Attachment
          ↓
       Open file
```

This is useful because an email may contain important evidence.

For example:

```text
From: attacker
To: victim

Attachment:
invoice.exe
```

That would immediately be interesting.

---

# 22. Anomalies — 🚨 Important

The **Anomalies** tab shows suspicious activity detected by NetworkMiner's built-in detections.

Examples mentioned in your material include:

* EternalBlue exploitation
* Spoofing attempts

But there is an important warning:

> **NetworkMiner is NOT an IDS.**

### What is an IDS?

IDS = **Intrusion Detection System**

An IDS is designed specifically to detect suspicious or malicious activity.

NetworkMiner is primarily a:

```text
Network Forensic Analysis Tool
```

It simply has some built-in anomaly detections.

So don't think:

```text
NetworkMiner = IDS
```

Instead:

```text
NetworkMiner
      ↓
Network forensic analysis
      +
Some anomaly detection
```

---

# 23. Now connect ALL the tabs

This is the most important part.

Suppose you receive:

```text
suspicious.pcap
```

Don't randomly click tabs.

Use questions.

### Question 1: Who is involved?

Go to:

```text
HOSTS
```

Find:

```text
IP
MAC
OS
Ports
```

---

### Question 2: Who communicated with whom?

Go to:

```text
SESSIONS
```

Find:

```text
Client
Server
Ports
Protocol
Time
```

---

### Question 3: What domains were contacted?

Go to:

```text
DNS
```

Look for:

```text
Queries
Answers
Timestamps
```

---

### Question 4: Were credentials exposed?

Go to:

```text
CREDENTIALS
```

Look for:

```text
Usernames
Passwords
Hashes
Cookies
Authentication data
```

---

### Question 5: Were files transferred?

Go to:

```text
FILES
```

Look for:

```text
.exe
.pdf
.jpg
.zip
.html
etc.
```

---

### Question 6: Were images transferred?

Go to:

```text
IMAGES
```

Preview them.

---

### Question 7: What values were transmitted?

Go to:

```text
PARAMETERS
```

Look for:

```text
name=value
username=admin
id=123
etc.
```

---

### Question 8: Is there an interesting word?

Go to:

```text
KEYWORDS
```

Search:

```text
password
admin
secret
malware
etc.
```

---

### Question 9: Were emails/chats exchanged?

Go to:

```text
MESSAGES
```

Investigate:

```text
From
To
Time
Attachments
```

---

### Question 10: Did NetworkMiner detect something suspicious?

Go to:

```text
ANOMALIES
```

Look for:

```text
Spoofing
EternalBlue
Other detected anomalies
```

---

# 24. The mental map you should memorize

```text
                    PCAP
                     │
                     ▼
               NETWORKMINER
                     │
     ┌───────────────┼────────────────┐
     │               │                │
     ▼               ▼                ▼
   HOSTS          SESSIONS           DNS
     │               │                │
 Who is there?   Who talked?     What domains?
 OS / MAC        Ports           Queries
 Ports           Protocol        Answers
     │
     ├───────────────┐
     ▼               ▼
 CREDENTIALS       FILES
     │               │
 Auth data        Transferred files
 Hashes           Images/docs/etc.
 Cookies
     
     ▼
   IMAGES
     │
 Preview images
     
     ▼
 PARAMETERS
     │
 Name = Value
     
     ▼
 KEYWORDS
     │
 Search interesting words
     
     ▼
 MESSAGES
     │
 Emails / chats / attachments
     
     ▼
 ANOMALIES
     │
 Suspicious detections
```

---

# 25.  The easiest way to remember everything

Use this table during revision:

| Tab             | Main Question                                    |
| --------------- | ------------------------------------------------ |
| **Case**        | What PCAP am I investigating?                    |
| **Hosts**       | **Who is on the network?**                       |
| **Sessions**    | **Who talked to whom?**                          |
| **DNS**         | **What domains were queried?**                   |
| **Credentials** | **Were authentication details exposed?**         |
| **Files**       | **What files were transferred?**                 |
| **Images**      | **What images were transferred?**                |
| **Parameters**  | **What named values were transmitted?**          |
| **Keywords**    | **Does the traffic contain interesting words?**  |
| **Messages**    | **What emails/chats/messages were exchanged?**   |
| **Anomalies**   | **Did NetworkMiner detect suspicious activity?** |

------------------------
