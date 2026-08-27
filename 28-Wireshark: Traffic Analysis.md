##  Nmap scan types

| Scan            | Nmap command | Main thing to look for                  |
| --------------- | ------------ | --------------------------------------- |
| **TCP Connect** | `nmap -sT`   | Full TCP handshake                      |
| **SYN Scan**    | `nmap -sS`   | SYN → SYN/ACK → RST                     |
| **UDP Scan**    | `nmap -sU`   | UDP packet → ICMP error for closed port |

---

#  TCP Connect Scan — `-sT`

### What happens?

For an **open port**:

```text
Client                    Target
  |                         |
  |-------- SYN ----------->|
  |<------ SYN, ACK ---------|
  |-------- ACK ------------>|
```

The important point: **the TCP three-way handshake is completed.**

For a **closed port**:

```text
Client                    Target
  |                         |
  |-------- SYN ----------->|
  |<------ RST, ACK --------|
```

### Wireshark filter

```text
tcp.flags.syn==1 and tcp.flags.ack==0 and tcp.window_size > 1024
```

### Why `window_size > 1024`?

The TCP Connect scan actually tries to establish the connection, so the packet generally has a **larger TCP window size**.

### Remember

> **Connect scan = complete the handshake**

---

#  SYN Scan — `-sS`

This is often called a **half-open scan**.

It does **not** complete the TCP handshake.

### Open port

```text
Client                    Target
  |                         |
  |-------- SYN ----------->|
  |<------ SYN, ACK ---------|
  |-------- RST ------------>|
```

The scanner receives `SYN, ACK`, knows the port is open, and sends `RST` instead of completing the connection.

### Closed port

```text
Client                    Target
  |                         |
  |-------- SYN ----------->|
  |<------ RST, ACK --------|
```

### Wireshark filter

```text
tcp.flags.syn==1 and tcp.flags.ack==0 and tcp.window_size <= 1024
```

### Remember

> **SYN scan = SYN → SYN/ACK → RST**


#  UDP Scan — `-sU`

UDP does **not have a TCP-style three-way handshake**.

The scanner simply sends a UDP packet.

### Open UDP port

```text
Client                    Target
  |                         |
  |-------- UDP ----------->|
```

There might not be an immediate response.

### Closed UDP port

```text
Client                    Target
  |                         |
  |-------- UDP ----------->|
  |<--- ICMP Type 3 Code 3--|
```

The ICMP message means:

> **Destination Unreachable — Port Unreachable**

### Wireshark filter

```text
icmp.type==3 and icmp.code==3
```

### Remember

> **UDP closed port = ICMP Type 3, Code 3**

---

# Most Important Filters for the Lab

You can copy these into Wireshark one by one:

### General TCP

```text
tcp
```

### General UDP

```text
udp
```

### TCP Connect scan pattern

```text
tcp.flags.syn==1 and tcp.flags.ack==0 and tcp.window_size > 1024
```

### SYN scan pattern

```text
tcp.flags.syn==1 and tcp.flags.ack==0 and tcp.window_size <= 1024
```

### UDP closed-port pattern

```text
icmp.type==3 and icmp.code==3
```

-------------------
### 1. Global Search

| What you want | Wireshark filter | Meaning              |
| ------------- | ---------------- | -------------------- |
| TCP traffic   | `tcp`            | Show all TCP packets |
| UDP traffic   | `udp`            | Show all UDP packets |

---

### 2. SYN Flag

**Purpose:** Starts a TCP connection.

| Filter               | Meaning                              |
| -------------------- | ------------------------------------ |
| `tcp.flags == 2`     | **Only SYN** flag is set             |
| `tcp.flags.syn == 1` | SYN is set; other flags don't matter |

---

### 3. ACK Flag

**Purpose:** Acknowledges/accepts received data or a connection request.

| Filter               | Meaning                              |
| -------------------- | ------------------------------------ |
| `tcp.flags == 16`    | **Only ACK** flag is set             |
| `tcp.flags.ack == 1` | ACK is set; other flags don't matter |

---

### 4. SYN + ACK

**Purpose:** Server responds to a SYN when the port is open.

| Filter                                          | Meaning             |
| ----------------------------------------------- | ------------------- |
| `tcp.flags == 18`                               | **Only SYN + ACK**  |
| `(tcp.flags.syn == 1) and (tcp.flags.ack == 1)` | SYN and ACK are set |

### 5. RST Flag

**Purpose:** Resets/terminates a TCP connection.

| Filter                 | Meaning      |
| ---------------------- | ------------ |
| `tcp.flags == 4`       | **Only RST** |
| `tcp.flags.reset == 1` | RST is set   |


---

### 6. RST + ACK

**Purpose:** Indicates a reset while acknowledging the packet.

| Filter                                            | Meaning             |
| ------------------------------------------------- | ------------------- |
| `tcp.flags == 20`                                 | **Only RST + ACK**  |
| `(tcp.flags.reset == 1) and (tcp.flags.ack == 1)` | RST and ACK are set |

You will commonly see this when checking **closed TCP ports**.

---

### 7. FIN Flag

**Purpose:** Gracefully closes a TCP connection.

| Filter               | Meaning      |
| -------------------- | ------------ |
| `tcp.flags == 1`     | **Only FIN** |
| `tcp.flags.fin == 1` | FIN is set   |

-----------------------
# ARP Poisoning / Spoofing 

This section is teaching you **how to detect an ARP Spoofing / MITM attack in Wireshark**.

The main idea is:

> **Attacker lies about which MAC address belongs to an IP address.**

---

## 1. First understand ARP

ARP = **Address Resolution Protocol**

Its job is to find:

```text
IP Address  →  MAC Address
```

For example:

```text
192.168.1.1 → 50:78:b3:f3:cd:f4
```

The computer needs the MAC address because devices on the local network communicate using MAC addresses.

### Important ARP facts

* Works only on the **local network**
* Maps **IP → MAC**
* Not encrypted
* Has **no authentication**
* Not routable
* Uses ARP **requests and replies**

---

# 2. Normal ARP communication

Suppose your computer wants to find the MAC address of `192.168.1.1`.

### ARP Request

Your computer broadcasts:

```text
Who has 192.168.1.1?
Tell 192.168.1.25
```

Everyone on the LAN receives the request.

### ARP Reply

The device owning `192.168.1.1` responds:

```text
192.168.1.1 is at 50:78:b3:f3:cd:f4
```

So the computer learns:

```text
192.168.1.1
      ↓
50:78:b3:f3:cd:f4
```

---

# 3. Basic Wireshark filters

### Show all ARP traffic

```text
arp
```

### Show ARP requests

```text
arp.opcode == 1
```

Remember:

> **Opcode 1 = Request**

### Show ARP replies

```text
arp.opcode == 2
```

Remember:

> **Opcode 2 = Reply**

-------------------------

# ARP Spoofing 

## 1. First: What problem does ARP solve?

A computer knows the **IP address** of another device, but on a local network it ultimately needs the **MAC address** to deliver the Ethernet frame.

For example, your laptop wants to communicate with the router.

Your laptop knows:

```text
Router IP = 192.168.1.1
```

But it needs:

```text
Router MAC = 50:78:b3:f3:cd:f4
```

ARP is used to discover this.

---

# 2. Normal ARP — No Attacker

Imagine we have 3 devices:

```text
                    LOCAL NETWORK
                         |
       +-----------------+-----------------+
       |                 |                 |
    Victim             Router           Server
192.168.1.12        192.168.1.1       192.168.1.50
       |                 |                 |
    MAC-A              MAC-R             MAC-S
```

Let's give them simpler MAC names:

```text
Victim  = MAC-V
Router  = MAC-R
Attacker = MAC-A
```

The important thing is:

```text
192.168.1.1 → MAC-R
```

That means:

> "The device with IP `192.168.1.1` is using MAC `MAC-R`."

---

# 3. Victim wants to access the Internet

Suppose the victim wants to visit Google.

The victim sends traffic to its **default gateway**, which is:

```text
192.168.1.1
```

But before sending the Ethernet frame, the victim needs the router's MAC address.

So the victim sends an ARP request.

```text
             Victim
          192.168.1.12
                |
                |
                | ARP Request
                | "Who has 192.168.1.1?"
                |
                +---------------------> Broadcast
                                      |
                     +----------------+----------------+
                     |                                 |
                  Router                           Other devices
               192.168.1.1
                     |
                     |
                     | "I have 192.168.1.1"
                     | MAC = MAC-R
                     |
                     +-------------> Victim
```

The victim learns:

```text
192.168.1.1
      ↓
    MAC-R
```

It stores this information in its **ARP cache/table**.

---

# 4. Now the attacker enters

Suppose the attacker is:

```text
Attacker IP  = 192.168.1.25
Attacker MAC = MAC-A
```

The real network is still:

```text
Router:

192.168.1.1
     ↓
   MAC-R
```

The attacker wants the victim's traffic to come to **him first**.

So the attacker sends a **fake ARP message**.

---

# 5. What does the attacker lie about?

The attacker basically tells the victim:

> **"192.168.1.1 is at my MAC address."**

In simplified form:

```text
Attacker
192.168.1.25
MAC-A
    |
    |
    | FAKE ARP
    |
    | "192.168.1.1 = MAC-A"
    ↓
Victim
```

But this is FALSE.

The real information is:

```text
192.168.1.1 → MAC-R
```

The attacker is telling the victim:

```text
192.168.1.1 → MAC-A
```

So now the victim's ARP table may become:

```text
IP Address       MAC Address
--------------------------------
192.168.1.1  →   MAC-A     ← WRONG
```

Instead of:

```text
IP Address       MAC Address
--------------------------------
192.168.1.1  →   MAC-R     ← CORRECT
```

---

# 6. Why is this dangerous?

Now the victim wants to access the Internet again.

The victim thinks:

```text
Gateway IP = 192.168.1.1
```

It checks its ARP table:

```text
192.168.1.1 → MAC-A
```

So the victim sends the Ethernet frame to:

```text
MAC-A
```

which belongs to the attacker.

### Look at the traffic:

```text
BEFORE ARP SPOOFING

Victim
   |
   | Traffic
   |
   ↓
Router
```

After poisoning:

```text
AFTER ARP SPOOFING

Victim
192.168.1.12
   |
   | Traffic intended for gateway
   |
   ↓
Attacker
192.168.1.25
   |
   | Forward traffic
   |
   ↓
Router
192.168.1.1
```

**This is the important part.**

The victim still thinks it is communicating with the router.

But the traffic is actually reaching the attacker first.

---

# 7. Why is it called Man-in-the-Middle?

Because the attacker has placed themselves **between the victim and the router**.

Normal:

```text
Victim  -------------------->  Router
```

MITM:

```text
Victim  -------->  Attacker  --------> Router
                      ↑
                      |
                  Middle Man
```

The attacker is literally in the middle of the communication path.

That's why:

> **ARP Spoofing can be used to perform a Man-in-the-Middle (MITM) attack.**

---

# 8. But there is another side!

The attacker also needs the **router's traffic to the victim** to come through the attacker.

So the attacker can try to poison both sides.

### Victim's ARP table

Attacker tells victim:

```text
192.168.1.1 → MAC-A
```

Meaning:

> "I am the router."

### Router's ARP table

Attacker can tell the router:

```text
192.168.1.12 → MAC-A
```

Meaning:

> "I am the victim."

Now both sides believe the attacker's MAC belongs to the other device.

---


# 9. Where does ARP poisoning happen?

This is important:

ARP spoofing works on the **local network/LAN**.

For example:

```text
                    Wi-Fi / LAN
                         |
        +----------------+----------------+
        |                |                |
      Laptop           Attacker          Router
   192.168.1.12      192.168.1.25      192.168.1.1
```

The attacker needs to be on the same local network segment for this type of ARP manipulation.

---

--------------------------
# 5. The BIG warning sign: One IP → Two MAC addresses

This is one of the most important things to remember.

Normally:

```text
192.168.1.1 → MAC A
```

But you discover:

```text
192.168.1.1 → MAC A
192.168.1.1 → MAC B
```

 **Suspicious!**

It could indicate:

* ARP spoofing
* ARP poisoning
* Duplicate IP configuration
* Network problems

So you **investigate further** rather than immediately declaring it an attack.

---

# 6. Important Wireshark filters

### Detect duplicate IP/MAC situations

```text
arp.duplicate-address-detected or arp.duplicate-address-frame
```

Wireshark can flag situations where an IP address appears to be associated with different MAC addresses.

---

### ARP requests from a particular MAC

The example uses:

```text
((arp) && (arp.opcode == 1)) && (arp.src.hw_mac == target-mac-address)
```

Replace:

```text
target-mac-address
```

with the MAC address you're investigating.

For example:

```text
arp.opcode == 1 && arp.src.hw_mac == 00:0c:29:e2:18:b4
```

This lets you investigate **all ARP requests coming from that MAC**.

---

# 7. How the example identifies the attacker

The capture contains this MAC:

```text
00:0c:29:e2:18:b4
```

Initially it appears associated with:

```text
192.168.1.25
```

Then something suspicious happens.

The same MAC claims:

```text
192.168.1.1
```

And `192.168.1.1` is likely the gateway.

At the same time, another MAC already claims:

```text
192.168.1.1
     ↓
50:78:b3:f3:cd:f4
```

So we now have:

```text
192.168.1.1
     ↓
 ┌───────────────┐
 │               │
MAC A           MAC B
50:78...        00:0c...
```

🚨 This is a major ARP spoofing indicator.

---

# 8. ARP Flooding

Another suspicious behavior is a **large number of ARP requests**.

For example, the attacker sends requests involving:

```text
192.168.1.1
192.168.1.2
192.168.1.3
192.168.1.4
...
192.168.1.xxx
```

This can indicate:

> **ARP scanning/flooding**

But remember:

**ARP flooding ≠ automatically an attack.**

It could also be:

* Network discovery
* A legitimate scanning tool
* Misconfiguration
* Troubleshooting
* Malicious activity

You need to correlate it with other evidence.

---

# 9. The most important part: MAC addresses in HTTP traffic

This is where the investigation becomes much stronger.

At the IP level, HTTP traffic might look completely normal:

```text
Victim → Server
```

But IP addresses alone don't tell you what is happening at Layer 2.

So the analyst adds:

* Source MAC
* Destination MAC

as columns in Wireshark.

Then they discover:

> The suspicious MAC `00:0c:29:e2:18:b4` is appearing as the destination MAC for the victim's HTTP traffic.

That means traffic intended for the gateway is being sent through the suspicious machine.

This provides much stronger evidence of a possible **MITM attack**.

-------------------------

#  Identifying Hosts and Users

When investigating suspicious network traffic, knowing only:

```text
IP address → MAC address
```

is often **not enough**.

You want to answer:

```text
Who is this?
   ↓
Which computer?
   ↓
Which user?
   ↓
Which domain?
   ↓
Is this the suspicious machine/user?
```

For example:

```text
10.10.10.25
     ↓
MAC: 00:11:22:33:44:55
     ↓
Hostname: HR-PC-07
     ↓
User: john
     ↓
Domain: company.org
```

That gives the analyst much more useful information.

---

# 1. Three Protocols You Need to Know

The lesson focuses on three protocols:

| Protocol           | Mainly helps identify              |
| ------------------ | ---------------------------------- |
| **DHCP**           | Hostname, IP, MAC, domain          |
| **NBNS / NetBIOS** | Hostnames and IP information       |
| **Kerberos**       | Users, hostnames, domain, services |

Think of them like this:

```text
DHCP
 ↓
"What computer requested this IP?"

NBNS
 ↓
"What is this computer called?"

Kerberos
 ↓
"Which user/computer is authenticating?"
```

---

# 2. DHCP Analysis

## What is DHCP?

DHCP automatically gives a device network information.

For example:

```text
Laptop
   |
   | "I need an IP"
   ↓
DHCP Server
   |
   | "Use 192.168.1.25"
   ↓
Laptop
```

The DHCP server can provide:

```text
IP address
Subnet mask
Gateway
DNS server
Domain
Lease time
```

Most importantly for our investigation, DHCP traffic can contain the **hostname and MAC address**.

---

# 3. Start With the Global DHCP Filter

In Wireshark:

```text
dhcp
```

or:

```text
bootp
```

Why `bootp`?

DHCP is technically implemented using the BOOTP protocol framework, so Wireshark commonly displays DHCP packets under the `bootp` protocol.

So if:

```text
dhcp
```

doesn't give you what you expect, try:

```text
bootp
```

---

# 4. DHCP Packet Types

There are three important packet types in this lesson:

```text
DHCP REQUEST
      ↓
"What IP do I want?"

DHCP ACK
      ↓
"Okay, you can have it."

DHCP NAK
      ↓
"No, your request is rejected."
```

The important DHCP option is:

### Option 53 — DHCP Message Type

| DHCP type | Filter                  | Meaning               |
| --------- | ----------------------- | --------------------- |
| Request   | `dhcp.option.dhcp == 3` | Client requests an IP |
| ACK       | `dhcp.option.dhcp == 5` | Server accepts        |
| NAK       | `dhcp.option.dhcp == 6` | Server rejects        |

---

# 5. DHCP Request — Very Important

Use:

```text
dhcp.option.dhcp == 3
```

This shows DHCP Request packets.

Now look inside the packet.

You may see:

```text
Option: (12) Host Name
    Host Name: JOHN-PC

Option: (50) Requested IP Address
    Requested IP Address: 192.168.1.25

Option: (51) IP Address Lease Time
    Lease Time: 86400

Option: (61) Client Identifier
    MAC Address: 00:11:22:33:44:55
```

This is extremely useful.

---

# 6. DHCP Option 12 — Hostname

### Option 12 = Hostname

Example:

```text
Option 12
Hostname: JOHN-PC
```

Now you know:

```text
IP requested:
192.168.1.25

Hostname:
JOHN-PC
```

You can search for a particular hostname using:

```text
dhcp.option.hostname contains "JOHN"
```

For example:

```text
dhcp.option.hostname contains "HR"
```

could find:

```text
HR-PC-01
HR-LAPTOP
HR-ADMIN
```

---

# 7. DHCP Option 50 — Requested IP

### Option 50 = Requested IP address

Example:

```text
Option 50:
192.168.1.25
```

This tells you:

> "The client is asking for this IP address."

So you can connect:

```text
Hostname → Requested IP
```

Example:

```text
JOHN-PC
   ↓
192.168.1.25
```

---

# 8. DHCP Option 51 — Lease Time

### Option 51 = IP lease time

Example:

```text
Lease Time: 86400 seconds
```

That means the client can use the assigned IP for that period, subject to DHCP renewal.

For host identification, this is usually **less important** than hostname/IP/MAC.

---

# 9. DHCP Option 61 — Client Identifier

This can contain the client's MAC address or another client identifier.

Example:

```text
Client Identifier:
00:11:22:33:44:55
```

Now you can connect:

```text
Hostname
   ↓
JOHN-PC

IP
   ↓
192.168.1.25

MAC
   ↓
00:11:22:33:44:55
```

That's much better than looking at the IP alone.

---

# 10. DHCP ACK

Now use:

```text
dhcp.option.dhcp == 5
```

This shows **DHCP ACK** packets.

ACK means:

```text
Client:
"I want 192.168.1.25"

Server:
"Approved."
```

The ACK can provide useful information such as:

### Option 15 — Domain Name

Example:

```text
Domain Name:
company.org
```

Filter:

```text
dhcp.option.domain_name contains "company"
```

Now you might establish:

```text
Hostname: JOHN-PC
IP:       192.168.1.25
Domain:   company.org
```

---

# 11. DHCP NAK

Use:

```text
dhcp.option.dhcp == 6
```

NAK means:

```text
Client:
"I want this IP."

Server:
"No."
```

The useful option here is:

### Option 56 — Message

It may contain a reason such as:

```text
Requested address is not available
```

or another DHCP-specific rejection message.

The important point is:

**Don't blindly filter for a particular message. Read it.**

Because the actual reason can vary from investigation to investigation.

---

#  DHCP — Remember This

```text
DHCP REQUEST
│
├── Option 12 → Hostname
├── Option 50 → Requested IP
├── Option 51 → Lease time
└── Option 61 → Client identifier / MAC

DHCP ACK
│
├── Option 15 → Domain
└── Option 51 → Lease time

DHCP NAK
│
└── Option 56 → Rejection message
```

---

# 12. NetBIOS / NBNS

Now let's move to **NetBIOS Name Service (NBNS)**.

NBNS helps computers discover names on a network.

Think of it like asking:

```text
"What computer owns this name?"
```

For example:

```text
Who is WEB-PC?

        ↓

192.168.1.50
```

So NBNS can help associate:

```text
Hostname ↔ IP address
```

---

# 13. Start NBNS Investigation

Use:

```text
nbns
```

This displays NetBIOS Name Service traffic.

You may see information such as:

```text
Name: JOHN-PC
IP:   192.168.1.25
```

Now you have another way of identifying the host.

---

# 14. NBNS Name Filter

You can search for a specific name:

```text
nbns.name contains "JOHN"
```

For example:

```text
nbns.name contains "SERVER"
```

might reveal names such as:

```text
FILE-SERVER
SQL-SERVER
BACKUP-SERVER
```

This can be particularly useful when investigating suspicious traffic.

---

# 15. Why NBNS Is Useful

Suppose you see suspicious traffic:

```text
192.168.1.25 → 192.168.1.10
```

You don't know what `192.168.1.25` is.

You search NBNS:

```text
nbns
```

and find:

```text
192.168.1.25
      ↓
JOHN-PC
```

Now the investigation becomes:

```text
Suspicious IP
     ↓
192.168.1.25
     ↓
Hostname
     ↓
JOHN-PC
```

Much easier to investigate.

---

# 16. Kerberos

Now we get to one of the **most useful protocols for identifying users in Windows domains**.

Kerberos is an authentication protocol commonly used in Microsoft Windows domain environments.

Think:

```text
Computer/User
     |
     | "I want to access something"
     ↓
Kerberos
     |
     | Authentication / ticket
     ↓
Domain services
```

So Kerberos traffic can reveal **user and domain information**.

---

# 17. Start With:

```text
kerberos
```

This shows Kerberos traffic.

---

# 18. Finding Users

One important field is:

```text
CNameString
```

This can contain the client name.

For example:

```text
CNameString: john
```

That can identify a user.

Search for a specific name:

```text
kerberos.CNameString contains "john"
```

---

# 19. The `$` Trick — Very Important

This is one of the most important things to understand.

In Windows environments, computer accounts commonly end with:

```text
$
```

For example:

```text
JOHN-PC$
```

is generally a **computer account**.

Whereas:

```text
john
```

is a **user account**.

So:

```text
JOHN-PC$
```

→ computer/host

```text
john
```

→ user

Think:

```text
CNameString
      |
      +---- ends with $ ----> HOST
      |
      +---- no $ -----------> USER
```

---

# 20. Finding Only Usernames

The lesson gives:

```text
kerberos.CNameString and !(kerberos.CNameString contains "$")
```

Let's break it down.

### First part:

```text
kerberos.CNameString
```

means:

> Show packets containing the CNameString field.

### Second part:

```text
!( ... )
```

means:

> NOT.

So:

```text
!(kerberos.CNameString contains "$")
```

means:

> Exclude values containing `$`.

Therefore:

```text
kerberos.CNameString and !(kerberos.CNameString contains "$")
```

roughly gives you:

```text
User accounts
```

instead of computer accounts.

---

# 21. Kerberos Important Fields

There are several fields worth remembering.

| Field         | What it tells you            |
| ------------- | ---------------------------- |
| `CNameString` | Client/user or computer name |
| `pvno`        | Kerberos protocol version    |
| `realm`       | Kerberos domain/realm        |
| `sname`       | Service name                 |
| `addresses`   | Client address information   |

---

# 22. `pvno`

Filter:

```text
kerberos.pvno == 5
```

This looks for Kerberos version 5.

---

# 23. `realm`

The realm generally identifies the Kerberos authentication domain.

For example:

```text
REALM:
COMPANY.ORG
```

You can search:

```text
kerberos.realm contains ".org"
```

So you might discover:

```text
COMPANY.ORG
```

---

# 24. `sname`

The service name tells you what service the Kerberos ticket relates to.

The lesson gives:

```text
kerberos.SNameString == "krbtg"
```

This can help identify Kerberos ticket-related activity involving the Ticket Granting Service.

---

# 25. `addresses`

This field can provide client IP information and NetBIOS-related name information.

**Important:** according to the lesson, this information is available in **request packets**.

So when investigating a request, don't forget to inspect:

```text
addresses
```

---

#  Putting Everything Together

This is the really important part.

Imagine you discover suspicious traffic from:

```text
192.168.1.25
```

You don't know who owns it.

You can investigate step-by-step.

### Step 1 — DHCP

Search:

```text
dhcp
```

You discover:

```text
Hostname:
JOHN-PC

Requested IP:
192.168.1.25

MAC:
00:11:22:33:44:55
```

Now:

```text
192.168.1.25
      ↓
JOHN-PC
```

---

### Step 2 — NBNS

Search:

```text
nbns.name contains "JOHN"
```

You find:

```text
JOHN-PC
192.168.1.25
```

This provides another piece of evidence connecting the hostname to the IP.

---

### Step 3 — Kerberos

Search:

```text
kerberos.CNameString contains "john"
```

You might find:

```text
CNameString: john
```

Now you can potentially establish:

```text
IP
│
└── 192.168.1.25
        │
        ↓
Hostname
│
└── JOHN-PC
        │
        ↓
User
│
└── john
        │
        ↓
Domain
│
└── COMPANY.ORG
```

That's what **host and user identification** means in a network investigation.

---

#  The Three Protocols — Easy Memory Trick

Remember:

```text
             HOST / USER IDENTIFICATION
                        │
          ┌─────────────┼─────────────┐
          ↓             ↓             ↓
        DHCP          NBNS         Kerberos
          │             │             │
          ↓             ↓             ↓
      Hostname       Hostname        User
      IP              IP             Domain
      MAC                            Service
      Domain
```

### DHCP

> **"What computer got this IP?"**

### NBNS

> **"What is this computer called?"**

### Kerberos

> **"Which user/computer is authenticating?"**

--------------------------------

# What is tunnelling?

**Tunnelling = putting hidden data inside normal-looking network traffic.**

Think of:

```text
Normal envelope
     ↓
Looks harmless
     ↓
But contains a secret letter
```

In networking:

```text
Normal ICMP
     ↓
Ping
```

An attacker can instead do:

```text
ICMP packet
     ↓
Looks like normal ICMP
     +
Hidden attacker data
```

So the firewall may think:

> "It's just ICMP traffic. Nothing unusual."

But the attacker is actually using it to transfer data.

### Why attackers use it

They may hide:

* **Commands** from an attacker (C2)
* **Stolen data** leaving the network (exfiltration)

The two protocols we'll study are:

```text
ICMP → ping traffic
DNS  → domain-name lookup traffic
```
----------------
# What is ICMP Tunnelling?

##  What is ICMP normally used for?

You may have used:

```bash
ping 8.8.8.8
```

Ping uses **ICMP**.

The basic idea is:

```text
Your computer
     |
     | ICMP Echo Request
     ↓
  8.8.8.8
     |
     | ICMP Echo Reply
     ↓
Your computer
```

It's basically asking:

> **"Are you reachable?"**

---

##  So what is ICMP tunnelling?

ICMP packets can contain a **payload (extra data)**.

An attacker can abuse that space to put their own data inside the ICMP packet.

Instead of:

```text
ICMP packet
     ↓
Just normal ping-related data
```

the attacker sends:

```text
ICMP packet
     ↓
Normal-looking ICMP
     +
Hidden attacker data
```

So:

```text
Attacker/Malware
       |
       | ICMP
       | + hidden data
       ↓
    Internet
       |
       ↓
Attacker's server
```

That is **ICMP tunnelling**.

---

##  Simple analogy

Imagine someone sends you a postcard.

Normally:

```text
Postcard
   ↓
"Hello!"
```

But someone secretly writes important information in a hidden area of the postcard.

The postal system still sees:

> "It's just a postcard."

Similarly, the network sees:

> "It's just ICMP."

But there may be **additional data inside the packet**.

---

##  Why is this dangerous?

An attacker who has compromised a computer could use ICMP tunnelling to:

```text
Compromised computer
        |
        | hidden ICMP data
        ↓
Attacker's server
```

This could be used for:

### 1. C2 — Command and Control

The attacker sends commands to the infected computer.

```text
Attacker
   ↓
"Run this command"
   ↓
ICMP
   ↓
Victim
```

### 2. Data exfiltration

The infected computer sends stolen information back.

```text
Victim
   ↓
Sensitive data
   ↓
Hidden inside ICMP
   ↓
Attacker
```

---

##  What does a security analyst look for?

You don't automatically assume:

> "ICMP = attack."

ICMP is completely legitimate.

Instead, look for **unusual ICMP behaviour**.

For example:

```text
Normal:

PC
 ↓
small ICMP packet
 ↓
server

PC
 ↓
small ICMP packet
 ↓
server
```

But suspicious:

```text
PC
 ↓
ICMP + lots of data
 ↓
Unknown server

PC
 ↓
ICMP + lots of data
 ↓
Unknown server

PC
 ↓
ICMP + lots of data
 ↓
Unknown server
```

Things that can make ICMP suspicious include:

* **Unusually large packets**
* **Large amounts of ICMP traffic**
* **Unexpected destinations**
* **Data that appears to contain another protocol/data**

-------------------
# Thing 3: How to Find ICMP Traffic in Wireshark

Now we'll learn **just one thing: how to start investigating ICMP traffic**.

---

## 🟢 Step 1: Filter ICMP

In Wireshark, type:

```text
icmp
```

into the **Display Filter** bar.

Then press **Enter**.

You will see only ICMP packets:

```text
No.    Source          Destination       Protocol
---------------------------------------------------
15     192.168.1.10    8.8.8.8           ICMP
16     8.8.8.8         192.168.1.10      ICMP
17     192.168.1.10    8.8.8.8           ICMP
18     8.8.8.8         192.168.1.10      ICMP
```

---

## 🧠 Why do we do this?

Imagine the capture contains **50,000 packets**:

```text
TCP
UDP
DNS
HTTP
ARP
ICMP
TLS
...
```

It's difficult to investigate everything.

So we tell Wireshark:

> **"Show me only ICMP traffic."**

```text
50,000 packets
       ↓
   icmp filter
       ↓
  100 ICMP packets
```

Now we can investigate those packets.

---

# 🔍 What do we look at?

For ICMP tunnelling, one important thing is:

### **Packet size**

Normal ICMP packets are often relatively small.

If you suddenly see:

```text
ICMP
ICMP
ICMP
ICMP
```

with unusually large amounts of data, that is interesting.

For example:

```text
Normal:

ICMP packet
┌────────────────────┐
│ Header + small     │
│ amount of data     │
└────────────────────┘


Suspicious:

ICMP packet
┌──────────────────────────────────────────────┐
│ Header + LARGE amount of additional data    │
└──────────────────────────────────────────────┘
```

The important point is:

> **Large ICMP packets can be a clue that someone is using ICMP to carry additional data.**

But **large ≠ automatically malicious**.

You investigate further.

---

## 🎯 The filter from your lesson

Your lesson gives:

```text
data.len > 64 and icmp
```

Let's break it down:

### `icmp`

```text
icmp
```

means:

> Show ICMP packets.

### `data.len > 64`

```text
data.len > 64
```

means:

> Show packets where the data portion is larger than 64 bytes.

Together:

```text
data.len > 64 and icmp
```

means:

> **Show ICMP packets that contain more than 64 bytes of data.**

---

## 🧠 Your analyst thinking

Don't think:

> "This filter found something, therefore it's an attack."

Instead think:

```text
ICMP packet
     ↓
Data > 64 bytes
     ↓
Interesting?
     ↓
YES
     ↓
Investigate the packet
```

You might then inspect:

* Source IP
* Destination IP
* Packet size
* Packet frequency
* ICMP payload
* Whether the traffic is repeated
* Whether the destination is suspicious

---

### One important warning

An attacker can deliberately make packets look normal-sized.

So:

```text
Large ICMP packets
        ↓
Strong clue
```

but:

```text
Normal-sized ICMP packets
        ↓
Does NOT prove they're safe
```

That's why analysts look at **behaviour**, not just one number.

---

### 🎯 Remember

For now, remember just these two filters:

```text
icmp
```

→ **Show all ICMP traffic**

```text
data.len > 64 and icmp
```

→ **Show ICMP packets with more than 64 bytes of data**

That's it for this step.

------------------

# Thing 4: Looking Inside a Suspicious ICMP Packet

Now we already know how to find ICMP:

```text
icmp
```

And potentially large ICMP packets:

```text
data.len > 64 and icmp
```

Now let's learn **what to inspect inside one packet**.

---

## 🟢 Step 1: Click an ICMP packet

Suppose Wireshark shows:

```text
Source          Destination       Protocol
------------------------------------------------
192.168.1.25    10.10.10.5        ICMP
```

Click that packet.

In the middle panel, you'll see something like:

```text
Internet Protocol
    Source: 192.168.1.25
    Destination: 10.10.10.5

Internet Control Message Protocol
    Type: Echo Request
    Code: 0

Data
    ...
```

The important part for tunnelling is the **Data** section.

---

# 🧠 Why is the Data section important?

Normally, ICMP is used for things like:

```text
"Are you there?"
```

But an attacker can put additional information into the ICMP payload:

```text
ICMP Packet
┌─────────────────────────────┐
│ ICMP Header                 │
├─────────────────────────────┤
│ Data / Payload              │
│                             │
│ Hidden information          │
│ Hidden information          │
│ Hidden information          │
└─────────────────────────────┘
```

So we inspect the payload.

---

# 🔎 What might we find?

Sometimes the payload may look like random data:

```text
4a 6f 68 6e 00 92 af 31 ...
```

That doesn't automatically mean malware.

But sometimes you may notice something interesting.

For example:

```text
GET /command HTTP/1.1
```

or:

```text
SSH-2.0
```

or:

```text
POST /data
```

That would be interesting because we're seeing **signs of another protocol or application data inside ICMP**.

---

# 🚨 This is what your lesson means by:

> **"Encapsulated protocol signs in ICMP payload."**

In simple words:

> **Look inside the ICMP data and see whether it appears to contain information that normally belongs to another protocol.**

For example:

```text
Normal:

ICMP
 ↓
small data
```

Potentially suspicious:

```text
ICMP
 ↓
large payload
 ↓
contains HTTP-like information
```

or:

```text
ICMP
 ↓
payload
 ↓
contains SSH-like information
```

That doesn't prove tunnelling, but it gives you a reason to investigate.

---

# 🧩 Put the clues together

Imagine you find:

```text
ICMP
  ↓
Large payload
  ↓
Repeated every few seconds
  ↓
Always communicating with the same unknown IP
  ↓
Payload contains strange data
```

Now you have **multiple suspicious indicators**.

Compare that with:

```text
ICMP
  ↓
Small packet
  ↓
Occasional ping
  ↓
Known internal server
```

That looks much more normal.

---

## 🎯 The important lesson

Don't investigate ICMP using only **one clue**.

Think like this:

```text
             ICMP
               ↓
        ┌──────┴──────┐
        ↓             ↓
   Packet size    Destination
        ↓             ↓
     Payload      Frequency
        \             /
         \           /
          ↓         ↓
        Overall behaviour
               ↓
        Suspicious?
```

### Remember:

> **A large ICMP packet is a clue, not proof. The analyst needs to examine the payload and overall traffic behaviour.**

--------------


# Thing 5: What is DNS?

Before we learn **DNS tunnelling**, we need to understand **normal DNS**.

## 🟢 What does DNS do?

DNS converts a **domain name** into an **IP address**.

For example, you type:

```text
www.example.com
```

Your computer doesn't directly communicate using that name. It needs an IP address.

So your computer asks a DNS server:

```text
Computer
    |
    | "What is the IP of www.example.com?"
    ↓
DNS Server
    |
    | "It is 93.184.216.34"
    ↓
Computer
```

Then your computer can communicate with:

```text
93.184.216.34
```

---

# 🧠 Think of DNS like a phonebook

Imagine you have:

```text
Name: John
```

but you need John's phone number.

You look it up:

```text
John
 ↓
Phonebook
 ↓
9876543210
```

DNS does something similar:

```text
Website name
      ↓
DNS
      ↓
IP address
```

For example:

```text
google.com
    ↓
DNS
    ↓
IP address
```

That's why DNS is often called the **phonebook of the Internet**.

---

# 🔵 What does a normal DNS query look like?

Your computer might ask:

```text
What is the IP address of:

www.example.com
```

The DNS server responds with the IP.

So the traffic looks roughly like:

```text
Your computer
     |
     | DNS query
     | "www.example.com?"
     ↓
DNS server
     |
     | DNS response
     | "93.184.216.34"
     ↓
Your computer
```

This is completely normal.

---

# 🚨 Why is DNS interesting to attackers?

DNS is used **everywhere**.

Almost every computer constantly makes DNS requests.

For example:

```text
google.com
youtube.com
microsoft.com
github.com
...
```

Because DNS is so common, security systems have to allow a lot of it.

An attacker can abuse this.

Instead of sending:

```text
Normal DNS:

computer
   ↓
"google.com"
```

they can create strange DNS queries such as:

```text
computer
   ↓
"encoded-data.maliciousdomain.com"
```

The query **looks like DNS**, but the attacker has put information inside the domain name.

That is the basic idea behind **DNS tunnelling**.

---

## 🎯 One thing to remember

For now, remember only:

> **DNS normally converts domain names into IP addresses.**

And:

```text
Normal DNS:

website name
     ↓
   DNS
     ↓
IP address
```

Attackers can later abuse this normal DNS communication to **hide data or commands inside DNS traffic**.

-------------------
# Thing 6: What is DNS Tunnelling?

Now that you understand normal DNS, let's see how an attacker can abuse it.

## 🟢 Normal DNS

Normally, your computer asks:

```text
Computer
   |
   | "What is the IP of google.com?"
   ↓
DNS Server
   |
   | "Here is the IP"
   ↓
Computer
```

The domain name contains the **website name**.

---

# 🔴 DNS Tunnelling

In DNS tunnelling, the attacker puts **additional information inside the DNS query**.

For example, instead of:

```text
google.com
```

you might see something like:

```text
encoded-data.maliciousdomain.com
```

The important part is:

```text
encoded-data
      ↑
Hidden information
```

So the traffic still looks like a DNS request:

```text
Computer
    |
    | DNS query
    | encoded-data.maliciousdomain.com
    ↓
DNS Server
```

But the attacker is using the DNS query to **carry information**.

---

# 🧠 Simple analogy

Imagine sending a normal letter.

Normally:

```text
To: John
Message: Hello
```

But an attacker does:

```text
To: John
Message: ABC123XYZ987...
```

The postal system sees:

> "It's just a letter."

But the sender and receiver know that the strange text contains hidden information.

DNS tunnelling works similarly.

---

# 🚨 How can this be used?

There are two important possibilities.

### 1. Data exfiltration

The compromised computer can send stolen data to the attacker.

```text
Victim computer
      |
      | stolen data
      ↓
encoded-data.maliciousdomain.com
      |
      ↓
Attacker's DNS infrastructure
      |
      ↓
Attacker
```

### 2. Command and Control (C2)

The attacker can use DNS communication as a communication channel with malware.

Conceptually:

```text
Attacker
   |
   | command
   ↓
DNS infrastructure
   |
   | DNS response
   ↓
Compromised computer
```

So DNS becomes a communication channel between:

```text
Attacker ↔ Malware
```

---

# 🔍 Why can DNS tunnelling be difficult to notice?

Because DNS is **normal network activity**.

A security analyst expects to see:

```text
DNS
DNS
DNS
DNS
DNS
```

every day.

So the attacker tries to make their malicious traffic look like ordinary DNS.

The trick is:

```text
Normal DNS
     ↓
Looks normal

DNS tunnelling
     ↓
Also looks like DNS
     ↓
But contains hidden information
```

---

# 🚨 What should make you suspicious?

Suppose you see:

```text
google.com
microsoft.com
github.com
```

These look normal.

But suddenly:

```text
a8f92jd83jf92jd83jd.maliciousdomain.com
```

and then:

```text
92jd83jd92jd83jd92.maliciousdomain.com
```

and:

```text
kd83jd92jd83jd92jd.maliciousdomain.com
```

That is much more interesting.

Why?

Because the subdomains are:

* unusually long
* strange-looking
* potentially encoded
* repeatedly changing

That is one of the main clues for DNS tunnelling.

---

# 🎯 The main idea

Remember this:

> **DNS tunnelling = using DNS queries/responses to secretly carry data or commands.**

Normal:

```text
www.example.com
      ↓
     DNS
      ↓
   IP address
```

Potential tunnelling:

```text
encoded-data.maliciousdomain.com
             ↓
            DNS
             ↓
       Hidden information
```
--------------------
# Thing 7: Finding DNS Traffic in Wireshark

Now we'll start using Wireshark.

The first thing is very simple:

> **Tell Wireshark to show only DNS traffic.**

---

## 🟢 Step 1 — Use the `dns` filter

In Wireshark's **Display Filter** box, type:

```text
dns
```

Then press **Enter**.

You'll now see DNS packets only.

For example:

```text id="g3e0qk"
Source          Destination       Protocol
------------------------------------------------
192.168.1.20    192.168.1.1        DNS
192.168.1.20    192.168.1.1        DNS
192.168.1.20    192.168.1.1        DNS
192.168.1.20    192.168.1.1        DNS
```

---

# 🧠 Why do we start with `dns`?

Your capture could contain thousands of packets:

```text id="5w6j8v"
TCP
UDP
HTTP
TLS
ARP
DNS
ICMP
SMB
...
```

We don't want to investigate everything at once.

So:

```text id="6n0b8f"
All traffic
     ↓
    dns
     ↓
Only DNS traffic
```

Now we can concentrate on DNS behaviour.

---

# 🔍 What are we looking for?

For DNS tunnelling, one of the first things we examine is the **query name**.

Click a DNS packet.

You might see:

```text id="f6u7xk"
Domain Name System

Queries:
    Name: www.example.com
    Type: A
```

The important part is:

```text id="7p8zkm"
www.example.com
```

That's the domain being requested.

---

## 🟢 Normal DNS example

You might see:

```text id="7cxv5b"
www.google.com
www.microsoft.com
github.com
www.youtube.com
```

These are normal-looking domain names.

---

## 🔴 Potentially suspicious example

You might instead see:

```text id="8m1h2n"
a8f92jd83jf92jd83jd.maliciousdomain.com
```

The first part:

```text id="x1a0u5"
a8f92jd83jf92jd83jd
```

looks unusual.

It could potentially represent encoded information.

**Important:** strange-looking DNS names do **not automatically mean tunnelling**. We need more evidence.

---

# 🎯 The first analyst question

When you filter:

```text id="c1w0fu"
dns
```

don't immediately ask:

> "Is this malicious?"

Instead ask:

> **"What DNS names are being queried?"**

Then look for patterns.

For example:

```text id="x7b8m4"
Normal:

google.com
github.com
microsoft.com
```

versus:

```text id="z8q3yp"
Potentially suspicious:

a83jd92jd83jd.mydomain.com
92jd83jd92jd.mydomain.com
kd83jd92jd83jd.mydomain.com
```

The second pattern deserves further investigation.

---

----------------
# Thing 8: Unusually Long DNS Queries

Now we have:

```text
dns
```

which shows DNS traffic.

The **first clue** we'll learn is:

> **Is the DNS query name unusually long?**

---

## 🟢 Normal DNS

Think about normal domain names:

```text
google.com
github.com
youtube.com
microsoft.com
```

They're relatively short and readable.

A normal DNS query might look like:

```text
www.example.com
```

---

# 🔴 What can DNS tunnelling look like?

An attacker may put encoded information into the subdomain.

For example:

```text
a8f92jd83jf92jd83jd.maliciousdomain.com
```

Look at this part:

```text
a8f92jd83jf92jd83jd
^^^^^^^^^^^^^^^^^^^
     strange/long
```

The attacker may be using this section to carry data.

So you might see many queries like:

```text
a83jd92jd83.maliciousdomain.com
92jd83jd92.maliciousdomain.com
kd83jd92jd83.maliciousdomain.com
83jd92jd83j.maliciousdomain.com
```

Notice something?

The main domain stays the same:

```text
maliciousdomain.com
```

but the subdomain keeps changing.

That can be suspicious.

---

# 🧠 Why make the query long?

Because the attacker needs somewhere to put the hidden information.

Think of it like a message:

```text
Normal:

Hello
```

The attacker instead needs to transmit:

```text
This is some secret information...
```

They can encode pieces of that information into the DNS name:

```text
encoded-data-1.example.com
encoded-data-2.example.com
encoded-data-3.example.com
```

Each DNS query carries another piece.

---

# 🔎 Wireshark filter

Your lesson gives:

```text
dns.qry.name.len > 15 and !mdns
```

Let's understand it piece by piece.

### `dns.qry.name.len`

This means:

> Length of the DNS query name.

For example:

```text
google.com
```

has a relatively short query name.

### `> 15`

Means:

> Only show query names longer than 15 characters.

So:

```text
dns.qry.name.len > 15
```

means:

> **Show DNS queries whose name is longer than 15 characters.**

---

# 🟡 What does `!mdns` mean?

`mDNS` means **Multicast DNS**.

It is commonly used for local-network device/service discovery.

For example, devices may use mDNS to discover things on the local network.

We don't want those local queries to create unnecessary noise during this investigation.

So:

```text
!mdns
```

means:

> **Exclude mDNS traffic.**

Therefore:

```text
dns.qry.name.len > 15 and !mdns
```

means:

> **Show DNS queries with names longer than 15 characters, while excluding mDNS traffic.**

---

# 🚨 But remember!

This filter does **NOT** mean:

```text
Long DNS name = attack
```

That's wrong.

There are many legitimate long domain names.

For example:

```text
some-long-service-name.company.com
```

could be completely legitimate.

Instead:

```text
Long DNS name
      ↓
Interesting clue
      ↓
Investigate further
```

---

# 🎯 What should you look for?

After applying:

```text
dns.qry.name.len > 15 and !mdns
```

look at the actual query names.

### Normal-looking:

```text
login.microsoftonline.com
accounts.google.com
update.example.com
```

These are readable and make sense.

### More suspicious-looking:

```text
a8f92jd83jf92jd83jd.example.com
92jd83jd92jd83jd.example.com
kd83jd92jd83jd.example.com
```

Especially if:

```text
Same domain
     +
Many different long subdomains
     +
High number of requests
```

That becomes much more interesting.

---

## 🧠 Remember this

```text
dns.qry.name.len > 15 and !mdns
```

= **Find long DNS query names while excluding mDNS.**

And the analyst's thinking is:

```text
Long DNS query
      ↓
Is it normal?
      ↓
Readable and expected?
      ↓
Probably okay

OR

Random/encoded-looking?
      ↓
Repeated frequently?
      ↓
Same suspicious domain?
      ↓
Investigate for possible DNS tunnelling
```

----------------
# Thing 9: Encoded-Looking DNS Names

Now we know that **long DNS queries** can be a clue.

The next thing is to understand **why the DNS name might look strange or encoded**.

---

## 🟢 Normal DNS names

Normally, DNS names are meaningful:

```text
www.google.com
mail.company.com
login.microsoft.com
```

You can usually understand what the name represents.

For example:

```text
mail.company.com
    ↑
    └── probably mail service
```

---

# 🔴 What might DNS tunnelling look like?

An attacker may encode information and put it into a subdomain.

For example:

```text
aGVsbG8xMjM0.maliciousdomain.com
```

or:

```text
8f92jd83kd92jd83.maliciousdomain.com
```

The important part is:

```text
8f92jd83kd92jd83
^^^^^^^^^^^^^^^^
looks unusual
```

It doesn't look like a normal website/service name.

---

# 🧠 Why encode the data?

Suppose the attacker wants to send:

```text
HELLO
```

They could transform it into some encoded representation:

```text
HELLO
  ↓
encoded form
  ↓
SGVsbG8=
```

Then conceptually put part of that into a DNS name:

```text
SGVsbG8.maliciousdomain.com
```

The attacker controls the domain and can recover the encoded information on the other side.

So the DNS request is being used as a **data-carrying channel**.

---

# 📦 Think of it like packages

Imagine the attacker has a large file:

```text
SECRET INFORMATION
```

It's too large to put into one DNS query.

So they split it:

```text
Part 1 → abc123.example.com
Part 2 → def456.example.com
Part 3 → ghi789.example.com
```

The attacker receives those requests and puts the pieces back together.

Conceptually:

```text
Victim
  |
  | Part 1
  ↓
abc123.domain.com

Victim
  |
  | Part 2
  ↓
def456.domain.com

Victim
  |
  | Part 3
  ↓
ghi789.domain.com
```

The attacker can reconstruct:

```text
Part 1 + Part 2 + Part 3
          ↓
      Original data
```

---

# 🚨 What should an analyst notice?

Imagine you see:

```text
abc827dh92jd.domain.com
82jd92jd83kd.domain.com
jd83kd92jd83.domain.com
92kd83jd92kd.domain.com
```

Ask yourself:

### 1. Are the subdomains unusually long?

```text
YES
```

### 2. Do they look random or encoded?

```text
YES
```

### 3. Are they constantly changing?

```text
YES
```

### 4. Are they all going to the same domain?

```text
YES
```

Now you have a **stronger reason to investigate**.

---

# 🔍 Compare the two

### Normal:

```text
computer
   ↓
DNS
   ↓
mail.company.com
login.company.com
updates.company.com
```

The names have understandable purposes.

### Potential tunnelling:

```text
computer
   ↓
DNS
   ↓
a8f92jd83jd92.company.com
92jd83jd83kd92.company.com
kd92jd83jd92.company.com
83jd92kd83jd92.company.com
```

The subdomains look:

* Random
* Encoded
* Long
* Frequently changing

That pattern is much more interesting.

---

# 🧠 Important: Don't make this mistake

**Random-looking ≠ automatically malicious.**

Some legitimate applications generate random-looking subdomains.

So your conclusion should be:

❌ **"This is definitely DNS tunnelling."**

Instead:

✅ **"This DNS traffic has characteristics consistent with possible tunnelling and requires further investigation."**

That's how a security analyst should think.

---

## 🎯 Remember

The important pattern is:

```text
Long
  +
Random/encoded-looking
  +
Changing subdomains
  +
Same domain
  +
Many requests
       ↓
Possible DNS tunnelling
```

We've now learned **two major clues**:

```text
1. Unusually long DNS queries
2. Strange/encoded-looking subdomains
```
----------------------

# Cleartext Protocol Analysis — FTP Analysis

## 1. What is Cleartext Protocol Analysis?

A **cleartext protocol** is a network protocol where important information is transmitted **without encryption**.

For example, imagine an FTP login:

```text
Client
  |
  | USER admin
  | PASS password123
  v
FTP Server
```

If the traffic is captured with Wireshark, an analyst may be able to see:

```text
USER admin
PASS password123
```

That is the major security problem.

### Why is this important?

During an incident investigation, we don't just want to say:

> "I found FTP traffic."

We want to answer questions such as:

* Who communicated with the FTP server?
* Which usernames were used?
* Were login attempts successful?
* Were there failed login attempts?
* What directories were accessed?
* What files were listed?
* Was someone potentially brute-forcing the server?
* Was sensitive information transferred?

So **cleartext protocol analysis = finding useful security evidence from unencrypted network traffic.**

---

# 2. What is FTP?

**FTP = File Transfer Protocol**

FTP is designed primarily for transferring files between a client and a server.

Basic architecture:

```text
             FTP
       ┌──────────────┐
       │ FTP Client   │
       └──────┬───────┘
              │
              │ FTP commands
              │ USER
              │ PASS
              │ LIST
              │ CWD
              ↓
       ┌──────────────┐
       │ FTP Server   │
       └──────────────┘
```

The problem is that traditional FTP does **not provide encryption for the control connection**.

Therefore, sensitive information can potentially be observed in a packet capture.

---

# 3. Why is FTP Dangerous?

Because FTP can expose authentication and other information, an attacker who can observe the traffic may gain useful information.

The original material mentions several possible risks:

| Risk                    | Meaning                                                                                  |
| ----------------------- | ---------------------------------------------------------------------------------------- |
| **MITM attack**         | Attacker positions themselves between client and server and observes/manipulates traffic |
| **Credential stealing** | Username/password may be exposed                                                         |
| **Unauthorized access** | Stolen credentials may allow access                                                      |
| **Phishing**            | Information gathered from traffic can assist further attacks                             |
| **Malware planting**    | File-transfer access could potentially be abused                                         |
| **Data exfiltration**   | An attacker could use file-transfer mechanisms to move stolen data                       |

The important security idea is:

```text
Cleartext FTP
      ↓
Sensitive information visible
      ↓
Credentials / commands / activity exposed
      ↓
Possible security impact
```

---

# 4. The Most Important Idea: Wireshark Filters

When a packet capture contains thousands or millions of packets, we don't manually inspect everything.

Instead, we use **Wireshark display filters**.

For FTP, the basic filter is:

```text
ftp
```

This means:

> Show me packets that Wireshark has identified as FTP traffic.

Think of it as a first-level filter:

```text
Huge PCAP
   │
   │ ftp
   ↓
FTP traffic
   │
   ├── Login
   ├── Commands
   ├── Responses
   └── File/directory activity
```

---

# 5. FTP Response Codes

FTP responses contain **three-digit response codes**.

For example:

```text
220 Service ready
331 Username okay, need password
230 Login successful
530 Login incorrect
```

A very useful way to understand FTP response codes is to look at the **first digit**.

| First digit | General meaning                             |
| ----------- | ------------------------------------------- |
| **1xx**     | Information/request-related response        |
| **2xx**     | Successful completion                       |
| **3xx**     | Further information/authentication required |
| **4xx**     | Temporary failure                           |
| **5xx**     | Permanent failure/error                     |

The exact meaning depends on the specific response code.

### Important example

```text
230
```

means the user has successfully logged in.

So:

```text
230 → successful FTP login
```

---

# 6. FTP 1xx — Information Responses

The material focuses on these useful codes:

### 211 — System status

It can provide information about the FTP server's current system/status.

Wireshark:

```text
ftp.response.code == 211
```

---

### 212 — Directory status

Provides information related to directory status.

---

### 213 — File status

Provides information related to file status.

### Memory trick

```text
211 → System
212 → Directory
213 → File
```

You can remember:

> **211 → 212 → 213 = System → Directory → File**

These can be useful during investigation because they can reveal information about the FTP server and its files/directories.

---

# 7. FTP 2xx — Connection/Successful Responses

The material highlights:

### 220 — Service ready

Example:

```text
220 Service ready for new user
```

Meaning:

> The FTP server is ready to accept a connection/login.

---

### 227 — Entering Passive Mode

This is particularly useful when analyzing FTP data connections.

Filter:

```text
ftp.response.code == 227
```

---

### 228 — Long Passive Mode

Related to passive FTP operation.

---

### 229 — Extended Passive Mode

Another passive-mode response.

### Easy memory

```text
220 → Server ready

227 → Passive mode
228 → Long passive mode
229 → Extended passive mode
```

You don't need to memorize every FTP code immediately. Understand the important ones first.

---

# 8. FTP 3xx — Authentication-Related Responses

This is one of the **most important sections for security analysis**.

### 230 — Login successful

Example:

```text
230 User logged in
```

Meaning:

```text
Username + password
        ↓
Authentication successful
        ↓
230
```

Wireshark:

```text
ftp.response.code == 230
```

This can help you identify successful logins.

---

### 331 — Valid username / password required

Example:

```text
331 Username OK, need password
```

This usually means:

```text
USER admin
     ↓
331
     ↓
PASS ********
```

The username was accepted, and the server is asking for the password.

---

### 530 — Login failure

Example:

```text
530 Login incorrect
```

This is extremely useful during security investigations.

Filter:

```text
ftp.response.code == 530
```

This can reveal failed FTP authentication attempts.

---

# 9. The FTP Login Process

This is the most important flow to understand.

Imagine:

```text
Client                         FTP Server
  |                                |
  | -------- USER admin ---------> |
  |                                |
  | <----------- 331 ------------- |
  |                                |
  | ------ PASS password123 ------>|
  |                                |
  | <----------- 230 ------------- |
  |                                |
  |          LOGIN SUCCESS         |
```

Now compare that with a failed login:

```text
Client                         FTP Server
  |                                |
  | -------- USER admin ---------> |
  |                                |
  | <----------- 331 ------------- |
  |                                |
  | ------ PASS wrongpass -------->|
  |                                |
  | <----------- 530 ------------- |
  |                                |
  |          LOGIN FAILED          |
```

This is why FTP traffic can be extremely valuable during incident response.

---

# 10. FTP Commands

FTP clients communicate with the server using commands.

The most important commands from your material are:

| Command | Meaning                  |
| ------- | ------------------------ |
| `USER`  | Username                 |
| `PASS`  | Password                 |
| `CWD`   | Change working directory |
| `LIST`  | List directory contents  |

---

## USER

Example:

```text
USER admin
```

Meaning:

> The client is attempting to authenticate as `admin`.

Wireshark:

```text
ftp.request.command == "USER"
```

This allows you to find FTP username activity.

---

## PASS

Example:

```text
PASS password123
```

Meaning:

> The client is sending the FTP password.

Wireshark:

```text
ftp.request.command == "PASS"
```

Because traditional FTP sends this over an unencrypted control connection, it may be visible in a packet capture.

### Important security point

Never think:

> "I should expose someone's password."

Instead, as an analyst, the important concept is:

> **FTP authentication information can be exposed in cleartext traffic, making credential disclosure a serious security risk.**

---

# 11. CWD

`CWD` means:

> **Change Working Directory**

Example:

```text
CWD /home/user/documents
```

This tells the FTP server:

> Move into this directory.

This can help an analyst understand **where the user was operating inside the FTP server**.

---

# 12. LIST

`LIST` requests a directory listing.

For example:

```text
LIST
```

The server may respond with information about files/directories.

This can help answer:

> What files or directories was the FTP user looking at?

---

# 13. "Low-Hanging Fruits" — What Does That Mean?

This phrase is important.

In security analysis, **low-hanging fruits** means:

> Information that is relatively easy to extract and can immediately provide useful investigative clues.

For example:

```text
FTP traffic
   ↓
Search for USER
   ↓
Find usernames
   ↓
Search for 230
   ↓
Find successful logins
   ↓
Search for 530
   ↓
Find failed logins
```

You don't start by analyzing every packet manually.

You first extract the **high-value information**.

---

# 14. Finding Successful Logins

Use:

```text
ftp.response.code == 230
```

This means:

> Show FTP responses with code 230.

Since `230` represents successful login, this helps identify successful authentication events.

Example:

```text
192.168.1.10 → FTP Server
USER admin

FTP Server → 192.168.1.10
331

192.168.1.10 → FTP Server
PASS ********

FTP Server → 192.168.1.10
230
```

Investigation conclusion:

```text
Client: 192.168.1.10
Username: admin
Result: Successful login
```

---

# 15. Finding Failed Logins

Use:

```text
ftp.response.code == 530
```

This finds FTP authentication failures.

Example:

```text
USER admin
PASS password1
530

USER admin
PASS password2
530

USER admin
PASS password3
530

USER admin
PASS password4
230
```

An analyst might recognize:

```text
Several failures
       ↓
followed by
       ↓
successful login
```

That is worth investigating.

It **could** indicate:

* password guessing
* brute-force activity
* a legitimate user repeatedly entering the wrong password

So don't immediately conclude:

> "This is definitely an attack."

Instead:

> **This is a potential brute-force signal that requires correlation with other evidence.**

That's a very important SOC analyst mindset.

---

# 16. Detecting Brute Force

A brute-force attack attempts many passwords against an account.

Conceptually:

```text
Username: admin

password1 → FAIL
password2 → FAIL
password3 → FAIL
password4 → FAIL
password5 → FAIL
password6 → SUCCESS
```

In Wireshark:

```text
ftp.response.code == 530
```

can help you find the failed attempts.

Then investigate:

* Source IP
* Destination IP
* Username
* Time
* Number of attempts
* Whether a successful login followed

---

# 17. Finding the Target Username

The material gives:

```text
(ftp.response.code == 530) and
(ftp.response.arg contains "username")
```

The idea is:

> Find failed authentication responses associated with a particular username.

For example, conceptually:

```text
admin → failed
admin → failed
admin → failed
guest → failed
```

You can investigate whether one account is being repeatedly targeted.

---

# 18. Password Spraying

This is different from brute force.

### Brute force

One username:

```text
admin
 ↓
password1
password2
password3
password4
...
```

### Password spraying

One password:

```text
Password123
     ↓
admin
     ↓
guest
     ↓
john
     ↓
alice
     ↓
bob
```

So:

```text
Brute force
= MANY passwords → ONE account

Password spraying
= ONE/common password → MANY accounts
```

This distinction is **very important in security monitoring**.

---

# 19. Searching for a Specific FTP Password

The material gives:

```text
(ftp.request.command == "PASS") and
(ftp.request.arg == "password")
```

This means:

> Find FTP `PASS` commands where the password argument equals the specified value.

More generally:

```text
ftp.request.command == "PASS"
```

finds password commands.

Then you can inspect the associated traffic.

### Security note

In real investigations, treat captured credentials as sensitive evidence. Don't casually copy or share them.

---

# 20. Putting Everything Together

Now let's build the entire investigation process.

```text
                 FTP PCAP
                    |
                    v
              Filter: ftp
                    |
        ┌───────────┴───────────┐
        ↓                       ↓
   FTP Requests            FTP Responses
        |                       |
   ┌────┼────┐             ┌────┼─────┐
   ↓    ↓    ↓             ↓    ↓     ↓
 USER  PASS  CWD           220  230   530
 LIST                      227  331
                              
```

Then investigate:

```text
USER
 ↓
Which usernames?

PASS
 ↓
Authentication attempts?

230
 ↓
Successful login?

530
 ↓
Failed login?

CWD
 ↓
Which directories?

LIST
 ↓
Which files/directories were viewed?

227
 ↓
Passive FTP activity?
```

This is the core of FTP analysis.

---

# 21. Important Wireshark Filters

Here is the revision table you should remember.

| Purpose                           | Wireshark filter                                                        |
| --------------------------------- | ----------------------------------------------------------------------- |
| Show FTP traffic                  | `ftp`                                                                   |
| Find system-status responses      | `ftp.response.code == 211`                                              |
| Find passive mode                 | `ftp.response.code == 227`                                              |
| Find successful login             | `ftp.response.code == 230`                                              |
| Find username accepted            | `ftp.response.code == 331`                                              |
| Find failed login                 | `ftp.response.code == 530`                                              |
| Find USER commands                | `ftp.request.command == "USER"`                                         |
| Find PASS commands                | `ftp.request.command == "PASS"`                                         |
| Find a specific password argument | `ftp.request.arg == "password"`                                         |
| Find a particular failed username | `(ftp.response.code == 530) and (ftp.response.arg contains "username")` |

---

# 22. A Real Investigation Example

Suppose you have a PCAP containing:

```text
10.10.10.20 → FTP Server
USER admin

FTP Server → 10.10.10.20
331

10.10.10.20 → FTP Server
PASS wrong123

FTP Server → 10.10.10.20
530

10.10.10.20 → FTP Server
USER admin

FTP Server → 10.10.10.20
331

10.10.10.20 → FTP Server
PASS wrong456

FTP Server → 10.10.10.20
530

10.10.10.20 → FTP Server
USER admin

FTP Server → 10.10.10.20
331

10.10.10.20 → FTP Server
PASS correct123

FTP Server → 10.10.10.20
230
```

What can we conclude?

### Step 1 — Identify FTP

```text
ftp
```

### Step 2 — Find failed authentication

```text
ftp.response.code == 530
```

We see multiple failures.

### Step 3 — Find successful authentication

```text
ftp.response.code == 230
```

We see a successful login.

### Step 4 — Correlate

```text
admin
 ↓
failed
 ↓
failed
 ↓
successful
```

### Investigation result

> The capture shows multiple failed authentication attempts against the `admin` account followed by a successful login. This is a potential password-guessing/brute-force signal and should be correlated with source IP, timestamps, and subsequent FTP activity.

Notice the wording:

**"potential brute-force signal"**

rather than:

**"definitely a brute-force attack."**

That's how you should write security investigation findings.

---

# 23. What Does "Statistics and Key Results" Mean?

This is one of the most important ideas from the introduction.

Suppose a PCAP contains:

```text
10,000 FTP packets
```

Simply saying:

> "There is FTP traffic."

isn't very useful.

Instead, create useful findings:

```text
FTP connections: 12

Unique source IPs: 3

Successful logins: 4

Failed logins: 37

Targeted username: admin

Common source IP: 192.168.1.50

Observed directory activity: /documents
```

Now you have **investigative results**.

Think:

```text
Packets
   ↓
Filters
   ↓
Events
   ↓
Statistics
   ↓
Patterns
   ↓
Security conclusion
```

That is what professional network analysis is about.

---

# 24. The Analyst's Mindset

Don't just ask:

> "What does this packet say?"

Ask:

### WHO?

```text
Who is communicating?
Who is logging in?
Who is being targeted?
```

### WHAT?

```text
What commands are being executed?
What files/directories are accessed?
```

### WHEN?

```text
When did the activity happen?
How frequently?
```

### WHERE?

```text
Which source IP?
Which destination?
Which FTP directory?
```

### HOW?

```text
How did authentication happen?
How many failures?
Was there a successful login?
```

### WHY?

```text
Could this activity indicate:
- brute force?
- unauthorized access?
- data theft?
- suspicious file activity?
```

This is how you move from **packet analysis** to **incident analysis**.

---

# 25. One Very Important Concept: Follow the Sequence

Don't analyze FTP packets independently.

Look at the sequence:

```text
USER
 ↓
331
 ↓
PASS
 ↓
230
 ↓
CWD
 ↓
LIST
 ↓
RETR
```

This tells a story:

```text
Authentication
      ↓
Successful login
      ↓
Directory navigation
      ↓
File listing
      ↓
Potential file download
```

That sequence is much more valuable than looking at individual packets.

---

# 26. Easy Memory Map

Remember FTP analysis using this:

```text
                FTP ANALYSIS
                     |
        ┌────────────┼────────────┐
        ↓            ↓            ↓
     COMMANDS     RESPONSES     ACTIVITY
        |            |            |
   USER / PASS    230 / 530      CWD
   CWD / LIST    331 / 227       LIST
        |            |            |
        ↓            ↓            ↓
   Who is using?  Login result?  What are
                                they doing?
```

And the most important codes:

```text
220 → Server ready

227 → Passive mode

230 → LOGIN SUCCESS ✅

331 → Username accepted,
      password required

530 → LOGIN FAILURE ❌
```

----------------------

# HTTP Analysis 

The best way to understand HTTP analysis is to stop thinking of it as a list of Wireshark filters.

Think of the investigation like this:

```text
HTTP Traffic
     ↓
Who is talking?
     ↓
What is being requested?
     ↓
What did the server return?
     ↓
What information is inside the request?
     ↓
Does anything look abnormal?
     ↓
Could it indicate an attack?
```

---

# 1. What is HTTP?

**HTTP = Hypertext Transfer Protocol**

It is the protocol used for communication between a web client and a web server.

For example, when you visit:

```text
http://example.com
```

the communication roughly looks like:

```text
        Client / Browser
               |
               | HTTP Request
               | GET /index.html
               ↓
          Web Server
               |
               | HTTP Response
               | 200 OK
               ↓
        Client / Browser
```

So HTTP follows a **request-response model**.

### Client

The client asks for something:

```text
GET /login.html
```

### Server

The server responds:

```text
200 OK
```

and sends the requested content.

---

# 2. Why is HTTP Important for Security Analysts?

Traditional HTTP is **cleartext**.

That means the contents of HTTP communication can potentially be visible in a packet capture.

For example:

```text
GET /login.php?user=admin
Host: example.com
User-Agent: Mozilla/5.0
```

An analyst can potentially inspect:

* Requested URLs
* HTTP methods
* Hostnames
* User-Agent
* Server information
* Response codes
* Cleartext data
* Form data

Therefore HTTP traffic can provide a lot of evidence during an investigation.

---

# 3. What Attacks Can HTTP Analysis Help Detect?

The material mentions four important categories.

## Phishing pages

An attacker may create a fake login page.

HTTP analysis can reveal things such as:

```text
GET /login
POST /login
Host: suspicious-domain.example
```

---

## Web attacks

Examples include suspicious requests against web applications.

For example:

```text
GET /admin
GET /../../../
POST /login
```

The exact pattern depends on the attack.

---

## Data exfiltration

**Data exfiltration = unauthorized movement of data out of a system/network.**

An attacker may use HTTP to send information to an external server.

Conceptually:

```text
Victim
  |
  | HTTP POST
  | stolen information
  ↓
Attacker-controlled server
```

---

## Command and Control (C2)

C2 is communication between malware and an attacker's infrastructure.

For example:

```text
Victim machine
      |
      | HTTP request
      ↓
Attacker server
      |
      | command
      ↓
Victim machine
```

HTTP can be attractive to attackers because normal web traffic is common on networks.

---

# 4. HTTP vs HTTP/2

The material mentions:

```text
http
http2
```

HTTP/2 is a newer revision designed to improve web performance and communication efficiency.

For this section, remember the practical distinction:

```text
HTTP
 ↓
Traditional HTTP traffic

HTTP/2
 ↓
Newer HTTP version
 ↓
Binary framing + multiplexing
```

### What is multiplexing?

Instead of waiting for one request/response stream to finish before handling another, HTTP/2 can carry multiple streams over a connection.

Conceptually:

```text
HTTP/1.x

Request A → Response A
             ↓
Request B → Response B
             ↓
Request C → Response C
```

HTTP/2 can conceptually do:

```text
              One connection
                    |
       ┌────────────┼────────────┐
       ↓            ↓            ↓
    Stream A     Stream B     Stream C
       |            |            |
    Request      Request      Request
```

For your current Wireshark work, the important point is simply:

```text
http  → HTTP traffic
http2 → HTTP/2 traffic
```

---

# 5. HTTP Request Methods

A **request method** tells the server what the client wants to do.

The two most important ones here are:

```text
GET
POST
```

---

# 6. GET Request

`GET` is commonly used to **request/retrieve a resource**.

Example:

```text
GET /index.html HTTP/1.1
Host: example.com
```

Meaning:

> "Server, give me `/index.html`."

Wireshark:

```text
http.request.method == "GET"
```

This shows HTTP GET requests.

---

# 7. POST Request

`POST` is commonly used when the client sends data to the server.

For example, submitting a form:

```text
POST /login HTTP/1.1
Host: example.com

username=admin&password=test
```

Conceptually:

```text
GET
 ↓
Usually requesting something

POST
 ↓
Usually sending data to the server
```

Wireshark:

```text
http.request.method == "POST"
```

### Why is POST important in security analysis?

Because POST requests can carry data.

For example:

```text
POST /login
username=admin&password=test
```

or:

```text
POST /upload
file=data
```

Therefore POST traffic deserves attention during investigations.

---

# 8. Find All HTTP Requests

Use:

```text
http.request
```

This is useful when you want to see HTTP requests rather than focusing only on GET or POST.

Think:

```text
http
   ↓
All HTTP traffic

http.request
   ↓
HTTP requests

http.request.method == "GET"
   ↓
Only GET requests

http.request.method == "POST"
   ↓
Only POST requests
```

---

# 9. HTTP Response Status Codes

Now we move from:

> **What did the client ask?**

to:

> **What did the server say?**

The server responds with a **status code**.

Example:

```text
Client
  |
  | GET /index.html
  ↓
Server
  |
  | 200 OK
  ↓
Client
```

The status code tells us the result of the request.

---

# 10. 200 OK

```text
200 OK
```

Means:

> The request was successful.

Wireshark:

```text
http.response.code == 200
```

Example:

```text
GET /index.html
        ↓
200 OK
```

---

# 11. 301 — Moved Permanently

```text
301
```

Means:

> The requested resource has permanently moved to another location.

Example:

```text
GET /old-page
       ↓
301
       ↓
New location
```

Useful during investigation because redirects can show where traffic is being sent.

---

# 12. 302 — Temporary Redirect

```text
302
```

Means the resource is temporarily available at another location.

Difference:

```text
301 → Permanent redirect
302 → Temporary redirect
```

---

# 13. 400 — Bad Request

```text
400
```

Means:

> The server could not properly understand the request.

Example:

```text
Malformed / invalid request
          ↓
        400
```

Repeated unusual 400 responses can sometimes be useful when investigating malformed or probing traffic.

---

# 14. 401 — Unauthorized

```text
401
```

Means authentication is required.

Example:

```text
GET /admin
     ↓
401
     ↓
Authentication required
```

Wireshark:

```text
http.response.code == 401
```

This can be interesting when investigating attempts to access protected resources.

---

# 15. 403 — Forbidden

```text
403
```

Means:

> The server understood the request but refuses to allow access.

Example:

```text
GET /admin
     ↓
403 Forbidden
```

Wireshark:

```text
http.response.code == 403
```

---

# 16. 404 — Not Found

```text
404
```

Means:

> The requested resource could not be found.

Wireshark:

```text
http.response.code == 404
```

A single 404 is completely normal.

But imagine:

```text
GET /admin
404

GET /backup
404

GET /config
404

GET /phpmyadmin
404

GET /login
200
```

That pattern could indicate someone is **enumerating/probing the web server**.

This is an important security-analysis lesson:

> **One packet rarely tells the whole story. Patterns are more valuable.**

---

# 17. 405 — Method Not Allowed

```text
405
```

Means:

> The HTTP method used isn't allowed for that resource.

Example:

```text
PUT /login
     ↓
405 Method Not Allowed
```

Wireshark:

```text
http.response.code == 405
```

---

# 18. 408 — Request Timeout

```text
408
```

Means the server waited too long for the request.

Think:

```text
Client
   |
   | ...............
   | too slow
   ↓
Server
   |
   ↓
408
```

---

# 19. 500 — Internal Server Error

```text
500
```

Means:

> Something went wrong on the server while processing the request.

This is a server-side error.

---

# 20. 503 — Service Unavailable

```text
503
```

Means the server/service is currently unable to handle the request.

Possible reasons include:

* service unavailable
* overload
* maintenance
* backend failure

Wireshark:

```text
http.response.code == 503
```

---

# 21. Response Codes — Easy Memory

Don't try to memorize everything randomly.

Group them:

```text
2xx → SUCCESS
3xx → REDIRECTION
4xx → CLIENT/REQUEST PROBLEM
5xx → SERVER PROBLEM
```

For the important ones:

```text
200 → Success

301 → Permanent redirect
302 → Temporary redirect

400 → Bad request
401 → Authentication required
403 → Forbidden
404 → Not found
405 → Method not allowed
408 → Timeout

500 → Server error
503 → Service unavailable
```

---

# 22. HTTP Parameters — Finding Useful Information

Now we get to one of the most useful parts of HTTP analysis.

HTTP contains fields that tell us **who is communicating and what they're requesting**.

Important fields:

```text
User-Agent
Request URI
Full URI
Server
Host
Connection
```

Let's understand each.

---

# 23. User-Agent

The **User-Agent** tells the web server information about the software making the request.

For example:

```text
User-Agent: Mozilla/5.0
```

It can indicate things such as:

* Browser
* Operating system
* Client software

Example:

```text
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
```

Conceptually:

```text
Client
  |
  | "I am using this type of software"
  ↓
Server
```

Wireshark:

```text
http.user_agent
```

---

# 24. Why is User-Agent Useful for Security?

Attackers often try to make malicious traffic look normal.

For example:

```text
Mozilla/5.0
```

looks like normal browser traffic.

But sometimes the User-Agent may reveal a tool:

```text
sqlmap
Nmap
Nikto
Wfuzz
```

That is a strong clue.

For example:

```text
User-Agent: sqlmap/...
```

could indicate automated SQL injection testing or attack activity.

But remember:

> **User-Agent alone is not proof of an attack.**

An attacker can modify it.

---

# 25. Request URI

URI = **Uniform Resource Identifier**

A request URI identifies the resource being requested.

Example:

```text
GET /admin/login.php
```

Here:

```text
/admin/login.php
```

is the request URI.

Wireshark:

```text
http.request.uri
```

You can search for something interesting:

```text
http.request.uri contains "admin"
```

This means:

> Find HTTP requests where the URI contains `admin`.

Example matches:

```text
/admin
/admin/login
/admin/panel
/administrator
```

---

# 26. Full URI

The **full URI** contains more complete URI information.

Example:

```text
http://example.com/admin/login
```

Wireshark:

```text
http.request.full_uri
```

You can search:

```text
http.request.full_uri contains "admin"
```

Difference:

```text
Request URI:
 /admin/login

Full URI:
 http://example.com/admin/login
```

---

# 27. Server

The `Server` field can identify the server software.

Example:

```text
Server: Apache
```

Wireshark:

```text
http.server contains "apache"
```

This can provide useful information during reconnaissance or incident analysis.

---

# 28. Host

The `Host` header identifies the requested hostname.

Example:

```text
Host: example.com
```

Wireshark:

```text
http.host
```

Search for a specific host:

```text
http.host == "example.com"
```

Or partial matching:

```text
http.host contains "keyword"
```

This can help answer:

> Which website/domain was the client communicating with?

---

# 29. Connection

The `Connection` field provides connection-related information.

Example:

```text
Connection: Keep-Alive
```

Wireshark:

```text
http.connection == "Keep-Alive"
```

**Keep-Alive** generally means the connection can remain open for additional HTTP communication rather than closing immediately after one request/response.

---

# 30. Cleartext Data

One of the biggest advantages of analyzing traditional HTTP is that application data may be visible.

For example:

```text
POST /login

username=alice
password=test123
```

Or:

```text
GET /search?q=security
```

An analyst may search packet contents.

The provided material uses:

```text
data-text-lines contains "keyword"
```

This searches line-based text data for a particular keyword.

---

# 31. User-Agent Analysis

Now let's focus specifically on **User-Agent analysis**.

The attacker problem is:

```text
Attacker
   ↓
Uses HTTP
   ↓
Tries to make traffic look normal
   ↓
Analyst must identify anomalies
```

The User-Agent can sometimes provide a clue.

Start with:

```text
http.user_agent
```

This displays HTTP User-Agent-related traffic.

---

# 32. What Should You Look For?

The material gives several important anomaly patterns.

## 1. Different User-Agents from the same host

Imagine:

```text
192.168.1.50

10:00 → Mozilla/5.0
10:01 → Mozilla/5.0
10:02 → Mozilla/5.0
10:03 → sqlmap
```

That change is interesting.

Why?

Because one machine suddenly switched from ordinary-looking browser traffic to a security-testing tool.

---

# 33. Non-standard User-Agent

Example:

```text
User-Agent: MyCustomTool/1.0
```

This isn't automatically malicious.

But it deserves investigation.

Ask:

```text
Who created this?
Why is this host using it?
What requests are being made?
What happened before/after it?
```

---

# 34. Spelling Differences

This is a surprisingly useful technique.

Normal:

```text
Mozilla
```

Suspicious variants:

```text
Mozlilla
Mozlila
```

Why could this matter?

Attackers sometimes create custom tools that imitate legitimate software.

Therefore:

```text
Mozilla
≠
Mozlilla
```

A tiny spelling difference can be a clue.

---

# 35. Security Tools in User-Agent

Some tools may identify themselves.

The material mentions:

```text
Nmap
Nikto
Wfuzz
sqlmap
```

You can search for these:

```text
(http.user_agent contains "sqlmap") or
(http.user_agent contains "Nmap") or
(http.user_agent contains "Wfuzz") or
(http.user_agent contains "Nikto")
```

This is useful because these tools are commonly associated with scanning, enumeration, fuzzing, or web-security testing.

### But remember:

```text
Tool name detected
       ↓
Interesting clue
       ↓
NOT automatically malicious
       ↓
Correlate with traffic
```

For example, a security team may legitimately run `Nmap` during an authorized assessment.

---

# 36. Payload in User-Agent

Attackers can also place unusual data inside the User-Agent field.

For example, conceptually:

```text
User-Agent: <unusual payload>
```

So don't only ask:

> "What browser is this?"

Also ask:

> "Does this User-Agent contain something that looks abnormal?"

---

# 37. The Golden Rule of User-Agent Analysis

This is probably the **most important sentence in this section**:

> **Never whitelist a User-Agent just because it looks normal.**

Why?

Because the User-Agent can be changed.

An attacker can make malicious traffic claim:

```text
Mozilla/5.0
```

even if it wasn't generated by a normal browser.

Therefore:

```text
Normal-looking User-Agent
          ↓
        NOT
          ↓
Automatically trusted
```

Instead:

```text
User-Agent
    +
Source IP
    +
Destination
    +
URI
    +
HTTP method
    +
Response
    +
Timing
    ↓
Overall conclusion
```

This is called **correlation**.

---

# 38. Log4j Analysis

Now we reach a practical example.

Before hunting for a specific attack, a good analyst first understands what the attack is expected to look like.

The material uses **Log4j** as an example.

The important idea is:

```text
Research the threat
       ↓
Understand expected indicators
       ↓
Create Wireshark filters
       ↓
Search the PCAP
       ↓
Correlate findings
```

This is a very important SOC workflow.

---

# 39. What is Log4j?

Log4j is a Java logging library.

A critical vulnerability known as **Log4Shell** affected certain versions/configurations of Log4j.

For traffic analysis, you don't need to memorize the entire vulnerability right now.

You mainly need to understand:

> The attack can leave recognizable strings/patterns in network traffic.

The material highlights:

```text
jndi:ldap
```

and:

```text
Exploit.class
```

These can serve as investigation clues.

---

# 40. Why Search for Known Patterns?

Suppose threat research tells us that suspicious traffic may contain:

```text
jndi:ldap
```

Instead of manually reading thousands of packets:

```text
PCAP
 ↓
Search for known indicator
 ↓
jndi:ldap
 ↓
Potential suspicious packet
```

This is the same concept you learned with FTP.

```text
FTP:
Search 530 → failed login

HTTP:
Search known strings → possible attack
```

---

# 41. Log4j — POST Requests

The material says the attack starts with a POST request.

So you can begin with:

```text
http.request.method == "POST"
```

This narrows the investigation to POST traffic.

But remember:

> A POST request is **not automatically a Log4j attack**.

POST is extremely common in normal web traffic.

So:

```text
POST
 ↓
Candidate traffic
 ↓
Search for suspicious indicators
 ↓
Correlate
```

---

# 42. Searching for Log4j Indicators

The material provides:

```text
(frame contains "jndi") or (frame contains "Exploit")
```

The idea is:

> Search the entire packet/frame for known suspicious strings.

You can also use:

```text
(ip contains "jndi") or (ip contains "Exploit")
```

as given in the material, although conceptually, **`frame contains` is the more natural way to search packet contents for arbitrary text**.

The important lesson is not just the exact filter.

It's:

```text
Known attack pattern
        ↓
Search packet contents
        ↓
Find matching traffic
```

---

# 43. User-Agent and Log4j

The material also mentions searching User-Agent fields for unusual values such as:

```text
$
```

or:

```text
==
```

For example:

```text
(http.user_agent contains "$") or
(http.user_agent contains "==")
```

Why?

Because attackers may place unusual payloads or encoded/obfuscated content into HTTP fields.

Again:

> A `$` or `==` by itself does **not prove Log4j exploitation**.

It is simply an indicator worth investigating in context.

---

# 44. Complete HTTP Investigation Workflow

Now connect the whole lesson.

```text
                     HTTP PCAP
                         |
                         ↓
                    http / http2
                         |
          ┌──────────────┼──────────────┐
          ↓              ↓              ↓
       Requests       Responses       Fields
          |              |              |
      GET / POST     200 / 404       Host
          |          401 / 403       URI
          |          500 / 503       User-Agent
          |                           Server
          ↓
       Investigate
          |
          ↓
     Find anomalies
          |
          ↓
      Correlate data
          |
          ↓
   Security conclusion
```

---

# 45. Example Investigation

Imagine a host:

```text
192.168.1.25
```

makes these requests:

```text
GET /index.html
200

GET /admin
403

GET /backup
404

GET /config
404

GET /phpmyadmin
404

POST /login
200
```

And the User-Agent is:

```text
Mozilla/5.0
```

At first glance, it might look normal.

But now investigate further.

### Step 1

Look at requests:

```text
http.request
```

### Step 2

Look at GET requests:

```text
http.request.method == "GET"
```

### Step 3

Look at 404s:

```text
http.response.code == 404
```

### Step 4

Look at suspicious paths:

```text
http.request.uri contains "admin"
```

### Step 5

Inspect User-Agent:

```text
http.user_agent
```

### Step 6

Correlate everything.

You may discover:

```text
One host
   ↓
Requests many sensitive paths
   ↓
Many 404 responses
   ↓
Attempts /admin
   ↓
Attempts /backup
   ↓
Attempts /config
```

That could represent **web enumeration/probing**.

Not proof by itself—but definitely worth investigating.

-------------------------


# Decrypting HTTPS Traffic — Learn It + GitHub Revision Notes

This topic becomes much easier if you first understand **what encryption changes in Wireshark**.

With HTTP:

```text
Browser
   |
   | GET /login
   | username=admin
   ↓
Server
```

Wireshark can potentially see the actual HTTP contents.

With HTTPS:

```text
Browser
   |
   | 🔒 Encrypted HTTP
   ↓
   TLS
   ↓
Server
```

Wireshark can see that communication is happening, but **the actual HTTP contents are encrypted**.

The main skill in this section is learning how a **TLS key log file** allows Wireshark to decrypt a captured HTTPS session.

---

# 1. What is HTTPS?

**HTTPS = HTTP Secure**

HTTPS is essentially HTTP communication protected by **TLS (Transport Layer Security)**.

Think:

```text
HTTP
 ↓
Web communication

HTTPS
 ↓
HTTP
 +
TLS encryption
```

So:

```text
HTTP
  ↓
GET /login
  ↓
Readable

HTTPS
  ↓
Encrypted HTTP
  ↓
Not directly readable
```

---

# 2. Why Do We Need HTTPS?

HTTP sends information without encryption.

HTTPS protects communication against threats such as:

* Sniffing
* Interception
* Unauthorized observation
* Some forms of spoofing/MITM attacks

For example, with HTTP:

```text
Client
   |
   | username=alice
   | password=12345
   ↓
Server
```

Someone capable of capturing the traffic may be able to see the information.

With HTTPS:

```text
Client
   |
   | 🔒 encrypted data
   ↓
Server
```

The captured packets don't directly reveal the HTTP content.

---

# 3. But Why Does a Security Analyst Need to Decrypt HTTPS?

This is a very important security concept.

You might think:

> "Encryption is good, so why would an analyst want to decrypt it?"

Because attackers also use HTTPS.

For example:

```text
Malware
   |
   | HTTPS
   ↓
Attacker's C2 server
```

To the network, this may look like ordinary encrypted web traffic.

HTTPS can therefore hide:

* C2 communication
* Malicious requests
* Data exfiltration
* Malicious payloads
* Web activity

So during an authorized investigation, an analyst may need to decrypt the traffic to understand what actually happened.

---

# 4. What Does Wireshark See Without Decryption?

Suppose the browser sends:

```text
GET /login
Host: example.com
Cookie: abc123
```

With normal HTTP, Wireshark can potentially display:

```text
GET /login
Host: example.com
Cookie: abc123
```

With HTTPS, Wireshark may instead show something like:

```text
TLS Application Data
Encrypted Application Data
```

Conceptually:

```text
WITHOUT TLS

GET /login
Host: example.com
username=alice


WITH TLS

Encrypted TLS Data
xxxxxxxxxxxxxxxx
xxxxxxxxxxxxxxxx
xxxxxxxxxxxxxxxx
```

The important point:

> **Wireshark can capture the encrypted packets, but it cannot simply read the original HTTP contents.**

---

# 5. Very Important: Capturing ≠ Decrypting

This distinction is essential.

```text
Packet capture
     ↓
You have the encrypted packets
```

does **not** mean:

```text
Packet capture
     ↓
You automatically know the plaintext
```

You need the appropriate cryptographic session secrets.

That's where the **TLS key log file** comes in.

---

# 6. TLS Handshake

Before encrypted application data is exchanged, TLS performs a handshake.

A simplified view:

```text
Client                         Server
  |                              |
  | ------ Client Hello ------> |
  |                              |
  | <------ Server Hello ------ |
  |                              |
  |   TLS negotiation continues  |
  |                              |
  | ===== Encrypted traffic ===>|
```

The first two messages are especially important:

```text
Client Hello
Server Hello
```

---

# 7. Client Hello

The client begins the TLS handshake by sending a **Client Hello**.

Conceptually:

```text
Browser
   |
   | Client Hello
   ↓
Server
```

Wireshark filter:

```text
tls.handshake.type == 1
```

Meaning:

> Show TLS Client Hello packets.

---

# 8. Server Hello

The server responds with a **Server Hello**.

```text
Client
   |
   | Client Hello
   ↓
Server
   |
   | Server Hello
   ↓
Client
```

Wireshark:

```text
tls.handshake.type == 2
```

Meaning:

> Show TLS Server Hello packets.

---

# 9. Easy Memory Trick

Remember:

```text
TLS handshake:

1 → Client Hello
2 → Server Hello
```

So:

```text
tls.handshake.type == 1
          ↓
      Client Hello

tls.handshake.type == 2
          ↓
      Server Hello
```

This is one of the easiest things to memorize.

---

# 10. Why Are Client Hello and Server Hello Useful?

Suppose you have a large PCAP.

You want to know:

> "Which IP addresses are involved in TLS connections?"

You can filter:

```text
tls.handshake.type == 1
```

and:

```text
tls.handshake.type == 2
```

Then inspect:

```text
Source IP
Destination IP
```

For example:

```text
192.168.1.20
       |
       | Client Hello
       ↓
142.250.x.x
       |
       | Server Hello
       ↓
192.168.1.20
```

You now know that those two IP addresses participated in the TLS handshake.

---

# 11. Why Does the Material Use `http.request` Too?

The provided filters are:

### Client Hello

```text
(http.request or tls.handshake.type == 1) and !(ssdp)
```

### Server Hello

```text
(http.request or tls.handshake.type == 2) and !(ssdp)
```

This looks complicated, so let's break it down.

---

## First part

```text
tls.handshake.type == 1
```

means:

```text
Client Hello
```

And:

```text
tls.handshake.type == 2
```

means:

```text
Server Hello
```

---

## What is `http.request` doing?

It includes HTTP request traffic in the result.

So:

```text
(http.request or tls.handshake.type == 1)
```

means:

> Show HTTP requests OR TLS Client Hello packets.

---

# 12. What is SSDP?

The material mentions:

**SSDP = Simple Service Discovery Protocol**

SSDP is used for discovering network services/devices.

For example:

```text
Device
  |
  | "What services are available?"
  ↓
Network
```

Wireshark filter:

```text
ssdp
```

The filter:

```text
!(ssdp)
```

means:

> Exclude SSDP packets.

Remember:

```text
!
```

means **NOT** in a Wireshark filter.

So:

```text
!(ssdp)
```

means:

```text
NOT SSDP
```

---

# 13. The Most Important Part: TLS Key Log File

Now we reach the core of this lesson.

A **TLS key log file** is a text file containing session secrets that Wireshark can use to decrypt captured TLS traffic.

Think of it like this:

```text
Encrypted PCAP
      +
TLS session secrets
      ↓
Wireshark
      ↓
Decrypted traffic
```

Without the necessary secrets:

```text
PCAP
 ↓
🔒 Encrypted
```

With the appropriate key log:

```text
PCAP + Key Log
      ↓
Wireshark
      ↓
🔓 Decrypted
```

---

# 14. Why Is the Key Log Needed?

Imagine this:

```text
Browser                    Server
   |                         |
   | TLS handshake           |
   |------------------------>|
   |<------------------------|
   |                         |
   | Encrypted session       |
   |========================>|
```

The TLS session establishes cryptographic secrets.

Those secrets are what allow the session's encrypted data to be decrypted.

The browser can write the relevant secrets to a file:

```text
SSLKEYLOGFILE
```

Then Wireshark can use that file.

---

# 15. SSLKEYLOGFILE

The environment variable is commonly named:

```text
SSLKEYLOGFILE
```

The basic workflow is:

```text
Configure browser/environment
          ↓
Browser creates TLS session
          ↓
Session secrets are written
          ↓
SSLKEYLOGFILE
          ↓
Wireshark loads the key log
          ↓
Captured TLS traffic can be decrypted
```

---

# 16. VERY Important Timing Rule

This is one of the most important points in the entire lesson.

The key log must correspond to the TLS sessions you captured.

Therefore:

> **You need to capture the traffic while the browser is generating the key log entries.**

Think:

```text
Browser creates session
        ↓
Session keys generated
        ↓
Key log entry created
        ↓
Traffic captured
```

If you capture traffic today but don't have the appropriate session secrets:

```text
Yesterday:
Traffic captured
     ↓
Encrypted PCAP
```

and then later try to generate unrelated keys:

```text
Today:
New TLS session
     ↓
Different session secrets
```

those new keys won't magically decrypt yesterday's session.

---

# 17. Why Are TLS Keys Per Session?

A simplified concept is:

```text
Session 1
   ↓
Keys A

Session 2
   ↓
Keys B

Session 3
   ↓
Keys C
```

Therefore:

```text
PCAP Session 1
       +
Keys B
       ↓
❌ Won't match
```

But:

```text
PCAP Session 1
       +
Keys A
       ↓
✅ Can potentially decrypt
```

This is why the correct key log must be available **during the capture**.

---

# 18. Wireshark Decryption Workflow

The complete workflow is:

```text
1. Start packet capture
        ↓
2. Configure browser to create SSLKEYLOGFILE
        ↓
3. Visit HTTPS website
        ↓
4. Browser writes TLS session secrets
        ↓
5. Stop capture
        ↓
6. Open PCAP in Wireshark
        ↓
7. Give Wireshark the key log file
        ↓
8. Wireshark decrypts matching TLS sessions
        ↓
9. Analyze the recovered traffic
```

---

# 19. Adding the Key Log File in Wireshark

The material gives two approaches.

### Option 1 — Right-click

You can use the relevant Wireshark context menu to configure the TLS key log file.

### Option 2 — Preferences

Go through:

```text
Edit
  ↓
Preferences
  ↓
Protocols
  ↓
TLS
```

Then configure the key log file.

The exact UI can vary slightly between Wireshark versions, but the concept remains:

```text
Wireshark
   ↓
TLS preferences
   ↓
Key log file
```

---

# 20. Before Key Log vs After Key Log

This is the easiest way to understand what the key log accomplishes.

### Without key log

```text
Packet
 ↓
TLS
 ↓
Encrypted Application Data
 ↓
Cannot see original HTTP contents
```

### With matching key log

```text
Packet
 ↓
TLS
 ↓
Decrypt
 ↓
HTTP / HTTP2 information
 ↓
Readable application data
```

So the key log does **not** remove encryption from the network.

It gives Wireshark the information required to decrypt the captured session.

---

# 21. What Becomes Visible After Decryption?

This is where HTTPS analysis becomes very powerful.

Before:

```text
TLS Application Data
```

After successful decryption, you may see things such as:

```text
HTTP request
HTTP response
HTTP/2 details
Headers
Data
```

You may also see information such as:

```text
GET /login
Host: example.com
User-Agent: Mozilla/5.0
```

depending on the traffic and protocol.

---

# 22. HTTP/2 After Decryption

This is particularly important because modern websites may use HTTP/2.

Without decryption:

```text
TLS
 ↓
Encrypted application data
```

With decryption:

```text
TLS
 ↓
HTTP/2
 ↓
Headers
Streams
Requests
Responses
```

So decrypting HTTPS can expose **HTTP/2 packet details** that weren't understandable before.

---

# 23. Different Data Views in Wireshark

The material lists several representations you may encounter.

## Frame

The complete captured network frame.

Think:

```text
Frame
 ↓
Everything captured for that packet
```

---

## Decrypted TLS

The TLS layer after Wireshark has successfully decrypted it.

```text
Encrypted TLS
      ↓
Key log
      ↓
Decrypted TLS
```

---

## Decompressed Header

Some HTTP/2 header information may be compressed.

After processing, Wireshark may show:

```text
Decompressed Header
```

This allows the analyst to inspect the header information more easily.

---

## Reassembled TCP

TCP may split application data across multiple packets.

For example:

```text
Packet 1 → "GET /lo"
Packet 2 → "gin"
Packet 3 → " HTTP/1.1"
```

Wireshark can reassemble those pieces:

```text
GET /login HTTP/1.1
```

That's:

```text
Reassembled TCP
```

---

## Reassembled SSL

Similarly, encrypted TLS/SSL data may span multiple packets.

Wireshark can reassemble it for analysis.

---

# 24. Why Reassembly Matters

This is an important networking concept.

A large piece of application data doesn't necessarily fit into one packet.

For example:

```text
Application data:

"POST /login username=admin password=test"
```

may become:

```text
TCP Packet 1:
POST /log

TCP Packet 2:
in username=

TCP Packet 3:
admin password=

TCP Packet 4:
test
```

Wireshark needs to reconstruct the original stream:

```text
POST /login username=admin password=test
```

So:

```text
Packets
  ↓
TCP reassembly
  ↓
Complete data
```

---

# 25. A Simple HTTPS Investigation Example

Imagine you capture:

```text
192.168.1.10
       |
       | HTTPS
       ↓
example.com
```

Without a key log:

```text
Client Hello
Server Hello
Encrypted Application Data
Encrypted Application Data
Encrypted Application Data
```

You can identify:

```text
Client IP
Server IP
TLS handshake
Timing
Packet sizes
```

But you can't directly read the application content.

---

## Now add the matching key log

```text
PCAP
 +
SSLKEYLOGFILE
 ↓
Wireshark
```

Now you may see:

```text
Client
 ↓
GET /login
 ↓
Server

200 OK
 ↓
Response data
```

Now you can investigate the **actual application behavior**.

---

# 26. HTTPS Investigation Mindset

Don't stop after seeing:

```text
TLS
```

Ask:

### WHO?

```text
Who is the client?
Who is the server?
```

### WHEN?

```text
When did the TLS session start?
```

### WHAT?

```text
What HTTP/HTTP2 requests were made?
```

### WHERE?

```text
Which server/host was contacted?
```

### WHY?

```text
Could this be:
- normal browsing?
- malware C2?
- data exfiltration?
- suspicious web activity?
```

---

# 27. Why Attackers Like HTTPS

Imagine malware communicating with its C2 server.

Without encryption:

```text
Malware
   |
   | GET /command?id=123
   ↓
C2 Server
```

An analyst might immediately recognize the malicious communication.

With HTTPS:

```text
Malware
   |
   | 🔒 encrypted
   ↓
C2 Server
```

The content is hidden.

Therefore:

> **HTTPS is good for legitimate security, but attackers can also use the same protection to hide malicious traffic.**

This is why encrypted traffic analysis is important for SOC/IR analysts.

---

# 28. HTTP vs HTTPS — Connect Your Previous Lessons

You already learned HTTP analysis.

Now compare:

| HTTP                              | HTTPS                                            |
| --------------------------------- | ------------------------------------------------ |
| Cleartext                         | Encrypted                                        |
| `http` filter                     | `tls` / decrypted HTTP                           |
| Requests visible                  | Requests hidden until decrypted                  |
| URI visible                       | URI may be hidden from packet inspection         |
| Headers visible                   | Headers encrypted                                |
| Data visible                      | Data encrypted                                   |
| No key required to read plaintext | Matching session secrets required for decryption |

The investigation changes from:

```text
HTTP:

Filter
 ↓
Read request
 ↓
Analyze
```

to:

```text
HTTPS:

Find TLS session
 ↓
Obtain matching session secrets
 ↓
Configure Wireshark
 ↓
Decrypt
 ↓
Analyze HTTP/HTTP2
```

---

# 29. Most Important Wireshark Filters

Keep these for revision.

### General

```text
http.request
```

Find HTTP requests.

```text
tls
```

Find TLS traffic.

```text
ssdp
```

Find SSDP traffic.

---

### TLS Client Hello

```text
tls.handshake.type == 1
```

Remember:

```text
1 = Client Hello
```

---

### TLS Server Hello

```text
tls.handshake.type == 2
```

Remember:

```text
2 = Server Hello
```

---

### Client Hello with the provided combined filter

```text
(http.request or tls.handshake.type == 1) and !(ssdp)
```

### Server Hello with the provided combined filter

```text
(http.request or tls.handshake.type == 2) and !(ssdp)
```

---

# 30. The Key Log File — The Most Important Concept

Memorize this:

```text
Browser
   |
   | TLS session
   ↓
Session secrets
   |
   ↓
SSLKEYLOGFILE
   |
   ↓
Wireshark
   |
   ↓
Decrypt matching PCAP traffic
```

### And remember:

```text
NO MATCHING KEY
      ↓
Encrypted traffic
      ↓
Cannot decrypt

MATCHING KEY
      ↓
Encrypted traffic
      +
Session secrets
      ↓
Can potentially decrypt
```

---

# 31. Common Mistake

### Mistake:

> "I captured HTTPS traffic, so I can decrypt it later."

Not necessarily.

You need the appropriate session secrets.

Correct:

```text
Capture traffic
      +
Capture/generate matching TLS secrets
      ↓
Wireshark
      ↓
Decryption
```

The key log must correspond to the captured sessions.

---

# 32. Complete Mental Model

This is the section I recommend memorizing:

```text
                    HTTPS
                      |
                      ↓
                  HTTP + TLS
                      |
                      ↓
              TLS Handshake
                      |
            ┌─────────┴─────────┐
            ↓                   ↓
      Client Hello         Server Hello
            |                   |
           1                     2
            └─────────┬─────────┘
                      ↓
              Encrypted Traffic
                      |
                      ↓
              ┌───────────────┐
              │   PCAP        │
              └───────┬───────┘
                      |
               Matching key log
                      |
                      ↓
                 Wireshark
                      |
                      ↓
                  Decryption
                      |
          ┌───────────┼───────────┐
          ↓           ↓           ↓
       HTTP       HTTP/2       Headers
          |           |           |
          └───────────┼───────────┘
                      ↓
               Security Analysis
```

-----------------

# Bonus: Hunt Cleartext Credentials — Learn It + GitHub Revision Notes

This section connects **everything you've learned so far**.

You already learned:

* FTP can expose usernames/passwords.
* HTTP can expose credentials and form data.
* Wireshark can filter specific packets.
* But looking at packets one by one can be slow.

This section teaches you a faster way to **find cleartext credentials in a PCAP**.

---

# 1. What Are Cleartext Credentials?

**Cleartext credentials** are authentication details transmitted without encryption.

For example:

```text
Client
   |
   | USER admin
   | PASS password123
   ↓
Server
```

If the protocol is cleartext, the packet capture may contain:

```text
Username: admin
Password: password123
```

This is obviously sensitive information.

---

# 2. Why Is Hunting Credentials Difficult?

Imagine a PCAP contains:

```text
Packet 101 → USER admin
Packet 102 → PASS password1

Packet 205 → USER admin
Packet 206 → PASS password2

Packet 309 → USER admin
Packet 310 → PASS password3

Packet 414 → USER admin
Packet 415 → PASS password4
```

Looking at packets individually is tedious.

The analyst has to mentally reconstruct:

```text
admin → password1 → failed
admin → password2 → failed
admin → password3 → failed
admin → password4 → success
```

That's difficult in a large capture.

---

# 3. Why a List Is Better

Instead of manually looking through hundreds of packets, imagine Wireshark gives you:

```text
Protocol | Username | Password/Info
------------------------------------
FTP      | admin    | password1
FTP      | admin    | password2
FTP      | admin    | password3
FTP      | admin    | password4
```

Now the pattern becomes immediately visible.

You can quickly ask:

```text
Are there many attempts?
       ↓
Same username?
       ↓
Different passwords?
       ↓
Successful attempt eventually?
       ↓
Possible brute-force activity?
```

This is the main purpose of the feature.

---

# 4. Wireshark Credentials Feature

Wireshark provides a feature that can extract certain cleartext credentials from supported protocols.

Go to:

```text
Tools
  ↓
Credentials
```

This opens a credentials window.

Conceptually:

```text
Wireshark
   |
   └── Tools
         |
         └── Credentials
                |
                ↓
        Detected credentials
```

---

# 5. Which Protocols Are Supported?

According to the material, Wireshark dissectors can extract cleartext passwords from protocols including:

```text
FTP
HTTP
IMAP
POP
SMTP
```

Remember this list:

> **FTP, HTTP, IMAP, POP, SMTP**

These are the important protocols for this feature.

---

# 6. What Is a Dissector?

You will see the word **dissector** frequently when learning Wireshark.

A Wireshark dissector is essentially the component that understands a particular protocol and breaks its packets into meaningful fields.

Think:

```text
Raw packet
    ↓
Wireshark dissector
    ↓
Understands protocol
    ↓
Username
Password
Command
Response
etc.
```

For example:

```text
FTP packet
    ↓
FTP dissector
    ↓
USER admin
PASS password123
```

This is how Wireshark knows what different parts of the packet represent.

---

# 7. What Does the Credentials Window Show?

The material says the window provides information such as:

* Packet number
* Protocol
* Username
* Additional information

Conceptually:

```text
┌────────┬─────────┬──────────┬─────────────────┐
│ Packet │ Protocol│ Username │ Additional Info │
├────────┼─────────┼──────────┼─────────────────┤
│ 101    │ FTP     │ admin    │ ...             │
│ 205    │ FTP     │ admin    │ ...             │
│ 309    │ HTTP    │ alice    │ ...             │
└────────┴─────────┴──────────┴─────────────────┘
```

The exact columns can depend on the protocol and Wireshark version.

---

# 8. Why Is the Packet Number Important?

Suppose Wireshark shows:

```text
Packet 309
FTP
admin
```

You can click the packet number.

Wireshark jumps directly to that packet.

So:

```text
Credentials list
       ↓
Packet 309
       ↓
Original packet
       ↓
Inspect details
```

This is extremely useful during investigations.

---

# 9. Why Is the Username Clickable?

The username entry can also be clicked.

It can take you to the packet containing the username information.

For example:

```text
Credentials window

Username: admin
      ↓
     click
      ↓
USER admin packet
```

The material also notes that the additional information can identify the packet containing the username.

So you can move between:

```text
Password packet
      ↕
Username packet
```

This makes manual verification much easier.

---

# 10. Example: Finding a Brute-Force Pattern

Imagine the Credentials window shows:

```text
Packet 100   FTP   admin
Packet 105   FTP   admin
Packet 110   FTP   admin
Packet 115   FTP   admin
Packet 120   FTP   admin
```

You notice:

```text
Same username
      +
Many credential attempts
```

Now investigate the packets.

You may discover:

```text
admin + password1 → 530
admin + password2 → 530
admin + password3 → 530
admin + password4 → 530
admin + password5 → 230
```

That gives you:

```text
5 attempts
   ↓
4 failures
   ↓
1 success
```

Potentially suspicious.

But remember the lesson from the previous FTP section:

> **Multiple failed credentials do not automatically prove brute force.**

It could also be:

```text
User forgot password
       OR
Application retrying
       OR
Brute-force attack
```

You need context.

---

# 11. Credential Hunting ≠ Attack Detection

This distinction is **very important**.

The Credentials feature answers:

> **"Where are cleartext credentials?"**

It does **not automatically answer**:

> **"Is this an attack?"**

Think:

```text
Credentials feature
       ↓
Find credentials
       ↓
Investigate context
       ↓
Look at timestamps
       ↓
Look at source IP
       ↓
Look at destination
       ↓
Look at response codes
       ↓
Look for repeated attempts
       ↓
Determine whether behavior is suspicious
```

---

# 12. Example: Normal User

Suppose:

```text
10:00
alice → password wrong → FAIL

10:01
alice → correct password → SUCCESS
```

That's probably not enough evidence for brute force.

---

# 13. Example: Potential Brute Force

Now:

```text
10:00:01
admin → password1 → FAIL

10:00:02
admin → password2 → FAIL

10:00:03
admin → password3 → FAIL

10:00:04
admin → password4 → FAIL

10:00:05
admin → password5 → FAIL

10:00:06
admin → password6 → SUCCESS
```

This pattern is much more suspicious.

The important evidence is:

```text
High frequency
      +
Same account
      +
Many different passwords
      +
Repeated failures
      +
Potential success
```

---

# 14. Why the Feature Saves Time

Without the Credentials feature:

```text
1000 packets
    ↓
Manually inspect
    ↓
Find USER
    ↓
Find PASS
    ↓
Match them
    ↓
Repeat
```

With the feature:

```text
1000 packets
    ↓
Tools → Credentials
    ↓
Credential list
    ↓
Spot patterns quickly
    ↓
Jump to interesting packets
```

So the feature improves:

> **Investigation speed and visibility.**

---

# 15. But Don't Completely Trust It

This is probably the **most important warning** in this section.

The material explicitly says:

> Do not rely entirely on this feature.

Why?

Because it only works with **specific supported protocols and traffic patterns**.

It cannot magically detect every credential transmitted in every protocol.

Therefore:

```text
Credentials feature
       +
Manual Wireshark analysis
       ↓
Better investigation
```

NOT:

```text
Credentials feature
       ↓
Everything is detected
       ↓
Investigation complete ❌
```

---

# 16. Manual Verification Is Important

Suppose the Credentials window shows:

```text
FTP | admin
```

Don't immediately write:

> "Attack detected."

Instead:

### Step 1

Click the packet.

### Step 2

Inspect the actual packet.

### Step 3

Look at the surrounding packets.

### Step 4

Check:

```text
Source IP
Destination IP
Timestamp
Username
Authentication result
Commands
```

### Step 5

Determine the behavior.

---

# 17. Connect It With Your FTP Knowledge

You already learned these filters:

```text
ftp
```

```text
ftp.request.command == "USER"
```

```text
ftp.request.command == "PASS"
```

```text
ftp.response.code == 230
```

```text
ftp.response.code == 530
```

Now the Credentials feature provides another way to approach the same problem.

### Manual method

```text
ftp
 ↓
USER
 ↓
PASS
 ↓
230 / 530
 ↓
Correlate
```

### Credentials feature

```text
Tools
 ↓
Credentials
 ↓
Credential list
 ↓
Identify interesting entries
 ↓
Jump to packets
 ↓
Manual investigation
```

The second method is faster for **initial hunting**.

---

# 18. Connect It With HTTP

You also learned HTTP can contain cleartext form data.

For example:

```text
POST /login

username=admin
password=test123
```

A credentials-hunting workflow might be:

```text
HTTP
 ↓
Credentials feature
 ↓
Potential credential entry
 ↓
Click packet
 ↓
Inspect POST request
 ↓
Inspect surrounding traffic
```

So your previous HTTP knowledge becomes useful here too.

---

# 19. The Big SOC Analyst Workflow

This entire section teaches a very important SOC concept:

```text
                 PCAP
                   |
                   ↓
          Automated extraction
                   |
                   ↓
        Credentials feature
                   |
                   ↓
        Interesting credentials
                   |
                   ↓
          Manual verification
                   |
        ┌──────────┼──────────┐
        ↓          ↓          ↓
      Source      Time      Protocol
        ↓          ↓          ↓
        └──────────┼──────────┘
                   ↓
              Correlation
                   ↓
              Find pattern
                   ↓
          Suspicious or normal?
```

This is exactly how you should think about Wireshark.

---------------------------

# Bonus: Actionable Results! — Learn It + GitHub Revision Notes

This section answers an important question:

> **After I investigate a suspicious packet capture, what do I actually do with the result?**

So far, you've learned how to:

```text
Capture traffic
      ↓
Analyze packets
      ↓
Find anomalies
      ↓
Identify suspicious IPs / ports / MACs
      ↓
Create investigation notes
```

Now we move to the next step:

```text
Investigation
      ↓
Action
```

That's what **Actionable Results** means.

---

# 1. What Does "Actionable Result" Mean?

An **actionable result** is an investigation finding that can be turned into a security action.

For example, imagine you discover:

```text
Internal Host
192.168.1.50
      |
      | suspicious traffic
      ↓
203.0.113.50
```

After investigating, you determine that `203.0.113.50` is a malicious destination.

Your result isn't simply:

> "I found suspicious traffic."

A more actionable result is:

> "Block traffic from/to `203.0.113.50` at the firewall."

So:

```text
Finding
  ↓
Suspicious IP
  ↓
Firewall rule
  ↓
Block/allow traffic
```

---

# 2. Where Does Wireshark Help?

Wireshark is primarily a **packet analyzer**, not a firewall.

But it can help you **generate firewall ACL rules based on information found in the capture**.

The feature is:

```text
Tools
  ↓
Firewall ACL Rules
```

Conceptually:

```text
Wireshark
   |
   | Investigate PCAP
   ↓
Identify suspicious traffic
   ↓
Tools → Firewall ACL Rules
   ↓
Generate firewall rule
   ↓
Implement on firewall
```

---

# 3. Important: Wireshark Doesn't Become the Firewall

This is a very important distinction.

Wireshark:

```text
Analyze traffic
       +
Generate rule
```

It does **not** mean:

```text
Wireshark
   ↓
Automatically blocks attacker ❌
```

Instead:

```text
Wireshark
   ↓
Generates ACL rule
   ↓
Security administrator/firewall
   ↓
Implements rule
```

So the generated rule is intended for an **external firewall interface**.

---

# 4. What Is an ACL?

**ACL = Access Control List**

An ACL is a set of rules that controls network traffic.

For example:

```text
Rule 1:
Block 203.0.113.50

Rule 2:
Allow internal network

Rule 3:
Block suspicious port
```

Conceptually:

```text
Traffic
   |
   ↓
Firewall
   |
   ├── Rule 1 → BLOCK
   ├── Rule 2 → ALLOW
   └── Rule 3 → BLOCK
```

The firewall checks traffic against these rules.

---

# 5. Why Is This Useful to a Security Analyst?

Imagine you've analyzed a PCAP and found:

```text
Attacker IP:
203.0.113.50

Destination port:
445

MAC:
AA:BB:CC:DD:EE:FF
```

You may want to create a rule such as:

```text
Block traffic
from/to
203.0.113.50
```

Instead of manually writing the firewall syntax, Wireshark can generate a rule in a format supported by different firewall platforms.

This saves time and reduces syntax mistakes.

---

# 6. What Information Can Be Used?

The material says Wireshark can generate rules based on:

### IP address

Example:

```text
203.0.113.50
```

### Port

Example:

```text
TCP 445
```

### MAC address

Example:

```text
AA:BB:CC:DD:EE:FF
```

So think:

```text
Firewall ACL
      |
      ├── IP
      ├── Port
      └── MAC
```

---

# 7. Example Investigation

Imagine your PCAP shows:

```text
192.168.1.20
      |
      | TCP/443
      ↓
198.51.100.25
```

After investigation, you determine that the destination is suspicious.

Your investigation notes might say:

```text
Source: 192.168.1.20
Destination: 198.51.100.25
Protocol: TCP
Port: 443
Finding: Suspicious communication
```

Now you need an action.

Potential action:

```text
Block 198.51.100.25
```

Wireshark can help generate a corresponding firewall rule.

---

# 8. The Complete Investigation-to-Action Workflow

This is the most important diagram to remember:

```text
                 PCAP
                   |
                   ↓
             Analyze traffic
                   |
                   ↓
             Detect anomaly
                   |
                   ↓
          Identify indicator
                   |
        ┌──────────┼──────────┐
        ↓          ↓          ↓
       IP         Port       MAC
        |          |          |
        └──────────┼──────────┘
                   ↓
       Tools → Firewall ACL Rules
                   |
                   ↓
          Generate firewall rule
                   |
                   ↓
       External firewall interface
                   |
                   ↓
             Apply the rule
                   |
                   ↓
              Block/Allow
```

---

# 9. Supported Firewall Formats

According to the material, Wireshark can generate rules for several firewall platforms.

## 1. Netfilter / iptables

Commonly associated with Linux firewalling.

```text
Netfilter
   ↓
iptables
```

---

## 2. Cisco IOS

Wireshark supports:

```text
Cisco IOS
├── Standard ACL
└── Extended ACL
```

### Standard ACL

Generally focuses primarily on source IP-based filtering.

### Extended ACL

Can provide more detailed filtering, such as:

```text
Source
Destination
Protocol
Port
```

---

## 3. IP Filter

Wireshark supports:

```text
IP Filter
(ipfilter)
```

This is another firewall/filtering framework.

---

## 4. IPFirewall

Also called:

```text
ipfw
```

Wireshark can generate rules in this format.

---

## 5. Packet Filter

Commonly referred to as:

```text
pf
```

Another firewall packet-filtering system.

---

## 6. Windows Firewall

Wireshark supports Windows Firewall rule formats through:

```text
netsh
```

The material mentions:

```text
Windows Firewall
├── netsh new format
└── netsh old format
```

---

# 10. Easy Way to Memorize the List

Remember:

```text
Linux
 → iptables

Cisco
 → IOS ACL

IP Filter
 → ipfilter

IP Firewall
 → ipfw

Packet Filter
 → pf

Windows
 → netsh
```

Or simply:

> **iptables, Cisco IOS, ipfilter, ipfw, pf, netsh**

---

# 11. Why Different Formats?

Different firewall platforms use different syntax.

For example, conceptually:

```text
Firewall A
→ Rule syntax A

Firewall B
→ Rule syntax B

Firewall C
→ Rule syntax C
```

You don't want to manually translate:

```text
"Block this IP"
```

into every firewall's syntax.

Wireshark can generate the appropriate format.

Conceptually:

```text
Finding:
Block X.X.X.X
       |
       ↓
Wireshark
       |
 ┌─────┼────────┐
 ↓     ↓        ↓
Linux Cisco   Windows
 ↓     ↓        ↓
Rule A Rule B  Rule C
```

---

# 12. Why This Matters During Incident Response

Imagine you're investigating an active incident.

You identify a malicious destination:

```text
Malicious IP
      ↓
203.0.113.50
```

The organization needs to stop communication quickly.

The workflow can be:

```text
Detect
  ↓
Validate
  ↓
Identify malicious IP
  ↓
Generate ACL rule
  ↓
Firewall team implements
  ↓
Traffic blocked
```

This is much more useful than simply recording:

> "IP is suspicious."

That's why the result is called **actionable**.

---

# 13. But Be Careful Before Blocking

This is an important security-analyst habit.

**Do not automatically block something just because one packet looks suspicious.**

First:

```text
Suspicious packet
      ↓
Investigate
      ↓
Check context
      ↓
Correlate other traffic
      ↓
Confirm finding
      ↓
Determine impact
      ↓
Take action
```

Why?

Because an incorrect firewall rule could block:

* Legitimate users
* Business services
* Critical infrastructure
* Required communication

So:

> **Detection → Validation → Action**

not:

> **Detection → Immediate blocking**

---

# 14. Example: False Positive

Suppose you see:

```text
192.168.1.50
     ↓
203.0.113.10
```

and initially think:

> "This looks suspicious."

Before blocking it, investigate:

```text
Who owns the IP?
What service is running?
Is this normal for the organization?
What protocol?
What port?
How frequently?
What data?
```

You might discover it's actually:

```text
Legitimate cloud service
```

Therefore:

```text
Suspicious ≠ Confirmed malicious
```

This is a very important SOC principle.

---

# 15. Wireshark's Role in the Bigger Security Architecture

Think of your security tools like this:

```text
                 Network
                    |
                    ↓
             Packet Capture
                    |
                    ↓
                Wireshark
                    |
           ┌────────┴────────┐
           ↓                 ↓
       Analysis          Investigation
           |                 |
           ↓                 ↓
      Suspicious IP      Suspicious port
           |                 |
           └────────┬────────┘
                    ↓
             Actionable result
                    |
                    ↓
              Firewall / ACL
                    |
                    ↓
             Traffic control
```

Wireshark sits mainly on the **analysis/investigation** side.

The firewall sits on the **enforcement** side.

---

# 16. Wireshark Is Not an IDS

This connects directly to your earlier lesson.

You learned:

> **Wireshark is not an IDS.**

That still applies.

### IDS

```text
Traffic
  ↓
IDS
  ↓
Detect
  ↓
Alert
```

### Wireshark

```text
PCAP
  ↓
Analyst
  ↓
Investigate
  ↓
Understand traffic
  ↓
Generate useful evidence/rules
```

So Wireshark helps the analyst investigate and prepare actions, but it isn't a replacement for a dedicated IDS/IPS or firewall.

---

# 17. Very Important Security Concept: Evidence → Action

This entire bonus section can be summarized as:

```text
Evidence
   ↓
Analysis
   ↓
Finding
   ↓
Indicator
   ↓
Action
```

For example:

```text
PCAP
 ↓
Suspicious connection
 ↓
Malicious IP identified
 ↓
IP = 203.0.113.50
 ↓
Generate firewall ACL
 ↓
Firewall blocks traffic
```

That's an **actionable result**.

---

# 18. Quick Revision Table

| Concept           | Meaning                                    |
| ----------------- | ------------------------------------------ |
| Actionable result | Finding that can lead to a security action |
| ACL               | Access Control List                        |
| Wireshark menu    | `Tools → Firewall ACL Rules`               |
| IP-based rule     | Filter traffic using IP address            |
| Port-based rule   | Filter traffic using network port          |
| MAC-based rule    | Filter traffic using MAC address           |
| Wireshark role    | Analyze traffic and generate rules         |
| Firewall role     | Enforce the rule                           |
| Important caution | Validate before blocking                   |
| IDS?              | No, Wireshark is not an IDS                |

### Supported formats

| Firewall/platform | Format                |
| ----------------- | --------------------- |
| Linux             | Netfilter / iptables  |
| Cisco             | IOS Standard/Extended |
| IP Filter         | ipfilter              |
| IP Firewall       | ipfw                  |
| Packet Filter     | pf                    |
| Windows           | netsh                 |

---

# 19. One-Minute Revision

```text
ACTIONABLE RESULTS

Investigate PCAP
      ↓
Find anomaly
      ↓
Identify source/destination
      ↓
Identify IP / Port / MAC
      ↓
Tools → Firewall ACL Rules
      ↓
Generate firewall rule
      ↓
Implement on external firewall
      ↓
Block / Allow traffic
```

### Remember:

```text
Wireshark = Analysis
Firewall  = Enforcement
```

And:

> **Never treat a suspicious packet as automatically malicious. Validate the finding before creating a blocking rule.**

---

# 20. Final Mental Model

You have now reached the end of this Wireshark investigation workflow:

```text
                 WIRESHARK INVESTIGATION
                         |
                         ↓
                     PCAP FILE
                         |
                         ↓
                  Protocol Analysis
                         |
        ┌────────────────┼────────────────┐
        ↓                ↓                ↓
       FTP              HTTP             HTTPS
        ↓                ↓                ↓
   Credentials       Cleartext         TLS
   Commands           Requests        Decryption
        |                |                |
        └────────────────┼────────────────┘
                         ↓
                  Detect anomalies
                         ↓
                  Extract evidence
                         ↓
                Correlate information
                         ↓
                 Identify indicators
                         ↓
                  Actionable result
                         ↓
             Firewall ACL generation
                         ↓
              External firewall
                         ↓
                  Enforce action
```

----------------------
