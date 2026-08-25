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


