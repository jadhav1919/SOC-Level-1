This section is basically teaching you **how to use Wireshark Statistics to quickly understand a PCAP before doing detailed packet analysis**.

Think of it like this:

> **Statistics = first look at the whole network traffic.**
> It helps you decide **where to investigate next**.

## 1. Statistics Menu

In Wireshark:

**Statistics →** you will find several options.

The important ones here are:

| Option                 | What it tells you                   | Why useful                       |
| ---------------------- | ----------------------------------- | -------------------------------- |
| **Resolved Addresses** | IP ↔ hostname relationships         | Find domains/resources contacted |
| **Protocol Hierarchy** | Protocols used in the PCAP          | See HTTP, DNS, TCP, UDP, etc.    |
| **Conversations**      | Communication between two endpoints | Find who talked to whom          |
| **Endpoints**          | Unique IP/MAC/port endpoints        | Find all devices/services        |
| **DHCP**               | DHCP activity                       | Investigate IP assignment        |
| **DNS**                | DNS queries/responses               | Investigate domains              |
| **HTTP/2**             | HTTP/2 traffic details              | Investigate web traffic          |

---

# 2. Resolved Addresses

### Simple meaning

**Resolved Addresses tells you:**

> "Which IP addresses are associated with which hostnames/domains?"

For example:

```text
192.168.1.10 → workstation.local
142.250.x.x → google.com
```

Wireshark gets hostname information from **DNS answers inside the PCAP**.

### How to open

```text
Statistics
   ↓
Resolved Addresses
```

### Why a security analyst cares

Suppose you see:

```text
192.168.1.50 → suspicious-domain.com
```

Now you know that the machine contacted that domain.

You can then investigate:

* What DNS query was made?
* What IP did the domain resolve to?
* Did the machine communicate with that IP?
* What protocol was used afterward?
* Was data transferred?

### Important point

**Resolved Addresses does NOT magically perform a live DNS lookup.**

It primarily uses information available in the captured traffic.

---

# 3. Protocol Hierarchy

This is one of the **most useful Statistics options**.

It answers:

> **"What protocols are present in this PCAP, and how much traffic belongs to each?"**

Open:

```text
Statistics
   ↓
Protocol Hierarchy
```

You may see something like:

```text
Frame
 └── Ethernet
      └── IPv4
           ├── TCP
           │    ├── HTTP
           │    └── TLS
           │
           └── UDP
                └── DNS
```

The percentages and packet counts tell you how much traffic each protocol represents.

### Why useful?

Imagine your PCAP contains:

```text
DNS     → 500 packets
HTTP    → 1200 packets
TLS     → 3000 packets
FTP     → 20 packets
```

You immediately know:

> "There is FTP traffic. I should investigate that."

You don't have to manually inspect thousands of packets.

### Security analyst mindset

Look for protocols that are:

* unexpected
* unusual
* insecure
* rarely used
* related to the suspected attack

For example:

```text
HTTP
FTP
Telnet
SMB
DNS
RDP
SSH
```

Then right-click/filter the interesting protocol traffic.

---

# 4. Conversations

### Simple meaning

A **conversation = communication between two endpoints.**

For example:

```text
192.168.1.10  ↔  192.168.1.20
```

or:

```text
192.168.1.10:51532
       ↕
192.168.1.50:443
```

Open:

```text
Statistics
   ↓
Conversations
```

Wireshark provides conversations for:

* Ethernet
* IPv4
* IPv6
* TCP
* UDP

### Example

Suppose TCP Conversations shows:

```text
Source              Destination          Packets
192.168.1.10        8.8.8.8              120
192.168.1.10        192.168.1.20         500
192.168.1.10        10.10.10.50          2000
```

The third conversation has significantly more traffic.

That could be worth investigating.

### Important difference

**Conversation = communication relationship.**

It tells you:

> Who communicated with whom?

---

# 5. Endpoints

Endpoints are slightly different.

### Simple meaning

Endpoints answers:

> **"What unique devices/IPs/ports exist in this capture?"**

Open:

```text
Statistics
   ↓
Endpoints
```

You can examine:

* Ethernet
* IPv4
* IPv6
* TCP
* UDP

### Example

Suppose the IPv4 tab contains:

```text
192.168.1.10
192.168.1.20
192.168.1.50
8.8.8.8
```

These are the unique IP endpoints seen in the capture.

---

# 6. Conversations vs Endpoints

This is **very important for exams and practical work**.

| Conversations                             | Endpoints                         |
| ----------------------------------------- | --------------------------------- |
| Shows communication between two endpoints | Shows individual unique endpoints |
| Focuses on **who talked to whom**         | Focuses on **who exists**         |
| Example: A ↔ B                            | Example: A, B, C                  |
| Useful for investigating connections      | Useful for identifying hosts/IPs  |

### Easy way to remember

**Endpoint = person**

**Conversation = phone call between two people**

So:

```text
Endpoints:
A
B
C
D
```

But conversations:

```text
A ↔ B
A ↔ C
B ↔ D
```

---

# 7. MAC Address Name Resolution

Wireshark can make MAC addresses easier to understand.

A MAC address looks like:

```text
00:1A:2B:3C:4D:5E
```

The first three bytes can identify the manufacturer/OUI.

For example, Wireshark may identify the vendor associated with the MAC address.

In the **Endpoints** window, you can enable:

```text
Name resolution
```

This can turn an otherwise difficult MAC address into more understandable manufacturer information.

---

# 8. IP and Port Name Resolution

Wireshark can also resolve:

### IP addresses

Instead of:

```text
142.250.x.x
```

you may see a hostname such as:

```text
google.com
```

### Ports

Instead of:

```text
80
```

Wireshark may display:

```text
http
```

Instead of:

```text
443
```

you may see:

```text
https
```

These options can be configured through:

```text
Edit
 ↓
Preferences
 ↓
Name Resolution
```

### Important

Name resolution can make packet analysis easier to read, but **don't blindly trust names**. Always verify the underlying IP/port when investigating suspicious traffic.

---

# 9. GeoIP

Wireshark can also provide geographical information about IP addresses.

For example:

```text
Source IP → India
Destination IP → United States
```

This can help an analyst understand where external traffic may be located geographically.

But Wireshark needs a **GeoIP database**, such as a MaxMind database.

The configuration is under:

```text
Edit
 ↓
Preferences
 ↓
Name Resolution
 ↓
MaxMind database directories
```

### Important for your TryHackMe lab

The walkthrough specifically says:

> The lab machine doesn't have an active Internet connection.

So **don't worry if the GeoIP map doesn't work in the lab**.

For the exercise, focus on understanding:

```text
Resolved Addresses
Protocol Hierarchy
Conversations
Endpoints
```

-------------------

This section is about **Statistics for specific protocols**. The main idea is: instead of looking at every packet in the PCAP, you can ask Wireshark to summarize **only IPv4, IPv6, DNS, or HTTP traffic**.

---

# 1. IPv4 and IPv6 Statistics

Normally, Wireshark's statistics can contain both IPv4 and IPv6 traffic.

These options let you separate them.

Go to:

```text
Statistics
   ↓
IPv4 Statistics
```

or

```text
Statistics
   ↓
IPv6 Statistics
```

### What is IPv4?

IPv4 addresses look like:

```text
192.168.1.10
10.0.0.5
8.8.8.8
```

### What is IPv6?

IPv6 addresses look like:

```text
2001:4860:4860::8888
fe80::1
```

### Why is this useful?

Suppose your PCAP contains:

```text
IPv4 → 10,000 packets
IPv6 → 50 packets
```

You can investigate the IPv6 traffic separately.

For example, an organization may normally use IPv4, but suddenly you find an unexpected IPv6 connection. That could become an investigation point.

### Easy memory

> **IPv4 Statistics = investigate IPv4 traffic**
> **IPv6 Statistics = investigate IPv6 traffic**

---

# 2. DNS Statistics

DNS is extremely important in network analysis.

### First: What is DNS?

DNS converts a domain name into an IP address.

For example:

```text
google.com
     ↓
142.250.x.x
```

Instead of remembering an IP address, humans use:

```text
google.com
```

DNS performs the lookup.

---

## DNS Statistics in Wireshark

Open:

```text
Statistics
   ↓
DNS
```

Wireshark summarizes the DNS packets in the PCAP.

You can see information such as:

* **Queries**
* **Responses**
* **Query types**
* **Response codes (RCODE)**
* **Opcodes**
* **Classes**
* **Services**

---

## Important DNS terms

### Query

A computer asks:

> "What IP address belongs to example.com?"

Example:

```text
Client → DNS Server
"What is the IP of example.com?"
```

### Response

The DNS server answers:

```text
DNS Server → Client
"example.com = 93.184.x.x"
```

---

## Query Type

One important thing is the DNS record/query type.

For example:

| Type      | Meaning          |
| --------- | ---------------- |
| **A**     | IPv4 address     |
| **AAAA**  | IPv6 address     |
| **CNAME** | Alias            |
| **MX**    | Mail server      |
| **NS**    | Name server      |
| **TXT**   | Text information |

### Security example

If you see many unusual DNS queries such as:

```text
x1a92.example.com
k82jd.example.com
p91kf.example.com
m72qa.example.com
```

you may want to investigate further.

Large numbers of unusual DNS queries can sometimes be associated with things such as **malware communication or DNS tunneling**, although DNS alone is not proof of malicious activity.

---

# 3. HTTP Statistics

HTTP is another important protocol for security analysts because it is commonly used for web communication.

Open:

```text
Statistics
   ↓
HTTP
```

Wireshark will summarize HTTP traffic in the PCAP.

You can examine things such as:

* HTTP requests
* HTTP responses
* response codes
* requested resources
* request information

---

# HTTP Request

A client may send:

```text
GET /login.html HTTP/1.1
```

Meaning:

> "Give me `/login.html`."

For example:

```text
Client
   |
   | GET /index.html
   ↓
Web Server
```

---

# HTTP Response

The server responds with a status code.

Examples:

|    Code | Meaning            |
| ------: | ------------------ |
| **200** | Successful         |
| **301** | Redirect           |
| **302** | Temporary redirect |
| **400** | Bad request        |
| **401** | Unauthorized       |
| **403** | Forbidden          |
| **404** | Not found          |
| **500** | Server error       |

---

## Why HTTP Statistics are useful

Imagine the PCAP contains:

```text
200 → 500 requests
404 → 150 requests
403 → 80 requests
500 → 20 requests
```

You might investigate the large number of:

```text
404
```

because repeated requests for nonexistent files can sometimes indicate **directory/file enumeration**.

For example:

```text
GET /admin
GET /backup
GET /config
GET /robots.txt
GET /.git
```

The statistics help you notice the activity quickly.

--------------------------

# Wireshark Packet Filtering 

This section is **very important** because filtering is one of the main skills you use when investigating a PCAP.

The basic idea is:

> **A PCAP may contain thousands of packets. Filtering helps you show only the packets you care about.**

---

# 1. Two Types of Filters

Wireshark has **two different filter systems**:

| Filter             | When used                    | Main purpose                         |
| ------------------ | ---------------------------- | ------------------------------------ |
| **Capture Filter** | Before/during packet capture | Decide what traffic gets captured    |
| **Display Filter** | After packets are captured   | Decide what captured packets you see |

The most important difference:

### Capture Filter

```text
Network traffic
      ↓
Capture filter
      ↓
Only matching packets saved
```

### Display Filter

```text
All captured packets
      ↓
Display filter
      ↓
Only matching packets displayed
```

---

# 2. Capture Filters

Capture filters are used **before you start capturing traffic**.

For example:

```text
tcp port 80
```

means:

> Capture TCP traffic using port 80.

### Example

Suppose 10,000 packets are travelling across the network.

You use:

```text
tcp port 80
```

Wireshark captures only traffic matching that capture filter.

### ⚠️ Important

If the traffic you need doesn't match your capture filter, **it won't be captured**.

Therefore, you could miss important evidence.

That's why, when investigating an unknown situation, a common approach is:

> **Capture broadly → investigate later using display filters.**

---

# 3. Capture Filter Syntax

Capture filters commonly use:

### Scope

```text
host
net
port
portrange
```

### Direction

```text
src
dst
src or dst
src and dst
```

### Protocol

```text
ether
wlan
ip
ip6
arp
tcp
udp
```

---

## Example: TCP port 80

```text
tcp port 80
```

Meaning:

> Capture TCP traffic involving port 80.

---

## Example: Specific host

```text
host 192.168.1.10
```

Meaning:

> Capture traffic involving `192.168.1.10`.

---

## Example: Source host

```text
src host 192.168.1.10
```

Meaning:

> Capture traffic coming **from** `192.168.1.10`.

---

## Example: Destination host

```text
dst host 192.168.1.10
```

Meaning:

> Capture traffic going **to** `192.168.1.10`.

---

# 4. Display Filters

This is the filter system you'll use **a lot when analyzing PCAPs**.

Display filters don't delete packets.

They simply hide packets that don't match your filter.

For example:

```text
tcp.port == 80
```

means:

> Show packets where TCP port is 80.

The original PCAP still contains the other packets.

---

# 5. Capture vs Display Example

Suppose your PCAP contains:

```text
10,000 packets
```

You apply:

```text
tcp.port == 80
```

You might now see:

```text
350 packets
```

But the PCAP still contains all 10,000 packets.

That's the key difference.

---

# 6. ⚠️ Don't Mix Their Syntax

This is very important.

### Capture filter

```text
tcp port 80
```

### Display filter

```text
tcp.port == 80
```

They look similar, but they are **different filter languages**.

Don't use:

```text
tcp.port == 80
```

as a capture filter.

And don't assume:

```text
tcp port 80
```

is the correct display-filter syntax.

---

# 7. Comparison Operators

Display filters allow you to compare values.

## Equal

```text
==
```

Example:

```text
ip.src == 10.10.10.100
```

Meaning:

> Show packets whose source IP is `10.10.10.100`.

You can also write:

```text
ip.src eq 10.10.10.100
```

---

## Not equal

```text
!=
```

Example:

```text
ip.src != 10.10.10.100
```

Meaning:

> Show packets whose source IP isn't `10.10.10.100`.

---

## Greater than

```text
>
```

Example:

```text
ip.ttl > 250
```

Meaning:

> Show packets where TTL is greater than 250.

---

## Less than

```text
<
```

Example:

```text
ip.ttl < 10
```

Meaning:

> Show packets where TTL is less than 10.

---

## Greater than or equal

```text
>=
```

Example:

```text
ip.ttl >= 250
```

---

## Less than or equal

```text
<=
```

Example:

```text
ip.ttl <= 10
```

---

# 8. Decimal and Hexadecimal

Wireshark allows both decimal and hexadecimal values.

For example:

```text
ip.ttl > 250
```

and:

```text
ip.ttl > 0xFA
```

are equivalent because:

```text
0xFA = 250
```

So don't get confused when you see hexadecimal in filters.

---

# 9. Logical Operators

You can combine filters.

There are three important logical operators:

```text
AND
OR
NOT
```

You can also use:

```text
&&
||
!
```

---

# 10. AND

**AND means both conditions must be true.**

Example:

```text
ip.src == 10.10.10.100 && tcp.port == 80
```

Meaning:

> Show traffic from `10.10.10.100` involving TCP port 80.

Think:

```text
Condition 1
    AND
Condition 2
    ↓
Both must match
```

### ⚠️ Important correction to the example in the lesson

This:

```text
ip.src == 10.10.10.100 AND ip.src == 10.10.10.111
```

normally cannot match a single packet because one packet cannot have **two different source IP addresses simultaneously**.

For two possible source IPs, you want **OR**.

---

# 11. OR

**OR means either condition can be true.**

Example:

```text
ip.src == 10.10.10.100 || ip.src == 10.10.10.111
```

Meaning:

> Show packets coming from either of these two IP addresses.

This is very useful when investigating multiple hosts.

---

# 12. NOT

**NOT means exclude something.**

Example:

```text
!(ip.src == 10.10.10.222)
```

Meaning:

> Show packets except those whose source is `10.10.10.222`.

---

# 13. Why `!=` Can Be Tricky

The lesson mentions:

```text
!=
```

can sometimes produce results you don't expect when a protocol field is absent.

The safer style for exclusion is often:

```text
!(expression)
```

For example:

```text
!(ip.src == 10.10.10.222)
```

The important thing for now is:

> **Use `!(...)` when you specifically want to negate a condition.**

---

# 14. Filter Toolbar Colors

When you type a display filter into Wireshark, the toolbar changes color.

### 🟢 Green

**Valid filter**

Example:

```text
ip.src == 10.10.10.100
```

Wireshark understands it.

---

### 🔴 Red

**Invalid filter**

There is a syntax/problem with the filter.

For example, you might have:

```text
ip.src = 10.10.10.100
```

instead of:

```text
ip.src == 10.10.10.100
```

---

### 🟡 Yellow

**Warning**

Wireshark understands the filter, but there may be an issue or unreliable/deprecated behavior.

So ideally:

> **Green = good**

---

# 15. Autocomplete

The Display Filter toolbar has autocomplete.

Start typing:

```text
ip.
```

and Wireshark will show available IP-related fields.

For example:

```text
ip.src
ip.dst
ip.ttl
ip.addr
```

The `.` is important because it lets you move deeper into protocol fields.

For example:

```text
tcp.
```

can show TCP-related fields.

Then:

```text
tcp.port
tcp.srcport
tcp.dstport
```

This is much easier than memorizing every field.

---

# 16. Very Useful Filters to Learn

For your Wireshark labs, remember these first:

### Find traffic from an IP

```text
ip.src == 10.10.10.100
```

### Find traffic going to an IP

```text
ip.dst == 10.10.10.100
```

### Find traffic involving an IP

```text
ip.addr == 10.10.10.100
```

### Find HTTP

```text
http
```

### Find DNS

```text
dns
```

### Find TCP

```text
tcp
```

### Find UDP

```text
udp
```

### Find TCP port 80

```text
tcp.port == 80
```

### Find TCP port 443

```text
tcp.port == 443
```

### Find a specific source and destination

```text
ip.src == 10.10.10.100 && ip.dst == 10.10.10.200
```

### Find either of two IPs

```text
ip.addr == 10.10.10.100 || ip.addr == 10.10.10.200
```

---

# 🧠 The Most Important Concept

Think of Wireshark filtering as asking questions.

Instead of:

> "Show me all 10,000 packets."

You ask:

> **"Show me packets from this IP."**

```text
ip.src == 10.10.10.100
```

Then:

> **"Show me only HTTP traffic from that IP."**

```text
ip.src == 10.10.10.100 && http
```

Then:

> **"Show me HTTP traffic going to this server."**

```text
ip.dst == 10.10.10.200 && http
```

Then you can investigate the matching packets in detail.

---

## 🔥 Quick Revision Table

| Concept            | Remember                     |        |                  |
| ------------------ | ---------------------------- | ------ | ---------------- |
| **Capture filter** | Controls what gets captured  |        |                  |
| **Display filter** | Controls what gets displayed |        |                  |
| Capture example    | `tcp port 80`                |        |                  |
| Display example    | `tcp.port == 80`             |        |                  |
| `==`               | Equal                        |        |                  |
| `!=`               | Not equal                    |        |                  |
| `>`                | Greater than                 |        |                  |
| `<`                | Less than                    |        |                  |
| `>=`               | Greater/equal                |        |                  |
| `<=`               | Less/equal                   |        |                  |
| `&&` / AND         | Both conditions              |        |                  |
| `                  |                              | ` / OR | Either condition |
| `!`                | NOT/exclude                  |        |                  |
| 🟢 Green           | Valid                        |        |                  |
| 🔴 Red             | Invalid                      |        |                  |
| 🟡 Yellow          | Warning                      |        |                  |

### ⭐ For your TryHackMe practical

Focus especially on these:

```text
ip.addr
ip.src
ip.dst
tcp
udp
tcp.port
dns
http
&&
||
!
==
```

Once you understand these, the next Wireshark filtering exercises become much easier.

-------------------
# Wireshark Advanced Filtering — Simple Explanation

This section takes you from **basic filtering** to **more precise searches**.

The idea is:

> Basic filters find packets by protocol/IP/port.
> **Advanced filters let you search inside packet fields and manipulate values.**

The important ones are:

```text
contains
matches
in
upper()
lower()
string()
```

---

# 1. `contains`

### What does it do?

`contains` searches for a specific value **inside a field**.

It is useful when you know part of a value but not the entire value.

Example:

```text
http.server contains "Apache"
```

Meaning:

> Show HTTP packets where the server field contains the word `Apache`.

For example:

```text
Server: Apache
Server: Apache/2.4.57
Server: Apache-Coyote
```

These can match because they contain:

```text
Apache
```

### ⚠️ Important

The lesson says `contains` is **case-sensitive**.

So:

```text
http.server contains "Apache"
```

may not match:

```text
apache
APACHE
```

That's where `upper()` or `lower()` becomes useful.

---

# 2. `matches`

`matches` is used for **regular expressions (regex)**.

This is more powerful than `contains` because you can describe a pattern.

Example:

```text
http.host matches "\.(php|html)"
```

Meaning:

> Find HTTP hosts containing `.php` or `.html` according to that regex pattern.

The important part is:

```text
\.(php|html)
```

Break it down:

```text
\.       → literal dot
php      → php
|        → OR
html     → html
```

So it is essentially looking for:

```text
.php
```

or:

```text
.html
```

### Easy difference

```text
contains → search for exact text
matches  → search using a pattern
```

---

# 3. `in`

`in` is useful when you have **multiple values** to check.

Example:

```text
tcp.port in {80 443 8080}
```

Meaning:

> Show TCP packets where the port is **80 OR 443 OR 8080**.

Instead of writing:

```text
tcp.port == 80 ||
tcp.port == 443 ||
tcp.port == 8080
```

you can simply use:

```text
tcp.port in {80 443 8080}
```

### Easy memory

> **`in` = is this value inside my list?**

For example:

```text
tcp.port in {21 22 80 443}
```

means:

```text
21 OR 22 OR 80 OR 443
```

---

# 4. `upper()`

`upper()` converts a string to **uppercase**.

Example:

```text
upper(http.server) contains "APACHE"
```

Suppose the packet contains:

```text
Apache
apache
APACHE
Apache/2.4
```

`upper()` converts the value to uppercase before searching.

For example:

```text
Apache
   ↓
APACHE
```

Then:

```text
contains "APACHE"
```

can find it.

### Why is this useful?

Because `contains` is case-sensitive.

So instead of worrying about:

```text
Apache
apache
APACHE
```

you can normalize the value first.

---

# 5. `lower()`

`lower()` does the opposite.

It converts a string to lowercase.

Example:

```text
lower(http.server) contains "apache"
```

Conceptually:

```text
Apache
   ↓
apache
```

Then Wireshark searches for:

```text
apache
```

### Easy memory

```text
upper() → UPPERCASE
lower() → lowercase
```

---

# 6. `string()`

Sometimes a Wireshark field isn't stored as a string.

For example:

```text
frame.number
```

is a numeric value.

You can convert it to a string using:

```text
string(frame.number)
```

Then you can use string operations such as `matches`.

Example:

```text
string(frame.number) matches "[13579]$"
```

### What does `[13579]$` mean?

This is a regular expression.

```text
[13579]
```

means:

> One of these digits: 1, 3, 5, 7, 9

And:

```text
$
```

means:

> End of the string.

Therefore:

```text
string(frame.number) matches "[13579]$"
```

means:

> Show packets whose frame number ends in an odd digit.

Example:

```text
1   ✅
2   ❌
3   ✅
4   ❌
15  ✅
16  ❌
```

---

# 7. Putting Them Together

The real power comes from combining these functions.

For example:

```text
upper(http.server) contains "APACHE"
```

This combines:

```text
upper()
   ↓
convert to uppercase
   ↓
contains
   ↓
search for APACHE
```

Another:

```text
string(frame.number) matches "[13579]$"
```

combines:

```text
string()
   ↓
convert number → text
   ↓
matches
   ↓
use regex
```

---

# 8. Quick Comparison

| Feature    | Purpose                       | Example                             |
| ---------- | ----------------------------- | ----------------------------------- |
| `contains` | Find text inside a field      | `http.server contains "Apache"`     |
| `matches`  | Search using regex            | `http.host matches "\.(php\|html)"` |
| `in`       | Check against multiple values | `tcp.port in {80 443 8080}`         |
| `upper()`  | Convert to uppercase          | `upper(http.server)`                |
| `lower()`  | Convert to lowercase          | `lower(http.server)`                |
| `string()` | Convert value to string       | `string(frame.number)`              |

---

# 9. Bookmarks

If you repeatedly use a complicated filter, you don't have to type it every time.

You can **save the filter as a bookmark**.

For example, suppose you frequently investigate HTTP POST requests:

```text
http.request.method == "POST"
```

You can save that filter.

Later, you can select the bookmark instead of typing the entire expression again.

### Why useful?

Imagine you're a security analyst investigating many PCAPs.

You might have favorite filters for:

```text
HTTP POST
DNS queries
TCP SYN
Suspicious IP
Failed HTTP responses
```

Saving them makes your workflow faster.

---

# 10. Filter Buttons

Wireshark also allows you to create **filter buttons**.

A filter button is basically:

> **One-click application of a saved display filter.**

For example:

```text
[ HTTP ] [ DNS ] [ POST ] [ Suspicious IP ]
```

Clicking one can immediately apply the associated filter.

### Bookmark vs Button

| Bookmark                      | Filter Button                    |
| ----------------------------- | -------------------------------- |
| Saves a filter                | Saves a filter                   |
| Select it when needed         | Click it directly                |
| Good for many complex filters | Good for frequently used filters |

---

# 11. Profiles

This is another very useful feature.

Imagine you have different types of investigations:

### Investigation 1 — Web Attack

You want:

```text
HTTP filters
HTTP coloring rules
Web-related buttons
```

### Investigation 2 — DNS Investigation

You want:

```text
DNS filters
DNS coloring rules
DNS buttons
```

### Investigation 3 — Malware Traffic

You want:

```text
DNS
HTTP
TCP
Suspicious IP filters
Special coloring rules
```

Changing all these settings manually every time would be annoying.

That's why Wireshark has **Profiles**.

---

# 12. What is a Wireshark Profile?

A profile stores a set of Wireshark configurations.

Think of it like:

```text
Profile: Web Investigation
 ├── Filters
 ├── Filter buttons
 ├── Coloring rules
 └── Preferences
```

Then:

```text
Profile: Malware Investigation
 ├── Filters
 ├── Filter buttons
 ├── Coloring rules
 └── Preferences
```

You can switch between them depending on the investigation.

---

# 13. Creating Profiles

The lesson gives:

```text
Edit
 ↓
Configuration Profiles
```

You can also access the profile selector from the **status bar** in the lower-right area of Wireshark.

From there you can create, modify, and switch profiles.

---

# 🔥 Practical Example

Suppose you're investigating whether a web server is running Apache.

### Step 1

Show HTTP:

```text
http
```

### Step 2

Search for Apache:

```text
http.server contains "Apache"
```

But remember `contains` is case-sensitive.

### Step 3

Make it less sensitive to capitalization:

```text
lower(http.server) contains "apache"
```

Now:

```text
Apache
APACHE
apache
Apache/2.4
```

can all become lowercase before the search.

---

# Another Example — Multiple Ports

Suppose you want:

```text
80
443
8080
```

Use:

```text
tcp.port in {80 443 8080}
```

Much cleaner than writing three separate conditions.

---

# Another Example — Find PHP/HTML

```text
http.host matches "\.(php|html)"
```

This uses regex to search for either:

```text
.php
```

or:

```text
.html
```

-----------------------------
