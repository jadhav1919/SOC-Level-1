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
