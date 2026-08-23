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
