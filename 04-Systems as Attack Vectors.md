# Systems: Attacks & Defences

# What is a System?

A **system** is any device, server, cloud service, or application that stores, processes, or provides access to data.

Examples:

* Laptop
* Desktop
* Web Server
* Mail Server
* Database Server
* Cloud Platform (Microsoft 365, AWS, Azure)
* Mobile Device

# Why Do Attackers Target Systems?

Systems often contain valuable information or provide access to other systems.

If attackers compromise one important system, they may gain access to an entire organisation.


## Examples

| Breached System           | Attacker's Goal                               |
| ------------------------- | --------------------------------------------- |
| Student's Laptop          | Steal gaming accounts, install botnet malware |
| IT Administrator's Laptop | Access the corporate network                  |
| Mail Server               | Steal thousands of emails                     |
| Industrial Control Server | Deploy ransomware and stop operations         |
| Government Website        | Deface the website or spread propaganda       |

> **Key Idea:** Compromising a **server** is usually far more valuable than compromising a single user account.


# Common Ways Systems Are Attacked

Most attacks begin by gaining initial access.

The three major attack methods are:

1. Human-led attacks
2. Software vulnerabilities
3. Supply chain attacks


# 1. Human-Led Attacks

Humans often unknowingly help attackers compromise systems.

## Common Examples

### Weak Passwords

Example:

```
Password: tryhackme
Password: 123456
Password: admin
```

Weak or reused passwords can be guessed, cracked, or found in previous data breaches.

### Password Reuse

Using the same password on multiple websites.

If one website is breached, attackers try the same credentials elsewhere (Credential Stuffing).


### Malicious USB Devices

Example:

A **Rubber Ducky** looks like a normal USB drive but behaves like a keyboard and automatically types malicious commands when plugged in.



### Downloading Pirated Software

Pirated software may contain:

* Trojans
* Spyware
* Ransomware
* Keyloggers

# Credential Stuffing

## Definition

An attack where stolen usernames and passwords from one breach are automatically tried on other websites or services.

### Example

```
Netflix Password
        │
        ▼
Email Login
        │
        ▼
Corporate VPN
        │
        ▼
Cloud Services
```

If passwords are reused, multiple accounts may be compromised.


# 2. Software Vulnerabilities

## Definition

A **software vulnerability** is a weakness or bug that attackers can exploit to gain unauthorised access or execute malicious actions.

Every software product may contain vulnerabilities.

Examples:

* Windows
* Linux
* Web Applications
* Mobile Apps
* Databases

# Zero-Day Vulnerability

## Definition

A **Zero-Day** is a vulnerability that is exploited **before the vendor has released a fix (patch)**.

The vendor has had **zero days** to prepare a defence.

These are among the most dangerous vulnerabilities because no official patch is initially available.

# CVE (Common Vulnerabilities and Exposures)

## Definition

A **CVE** is a unique identifier assigned to a publicly disclosed security vulnerability.

Example:

```
CVE-2021-34527
```

This identifier allows security professionals worldwide to reference the same vulnerability consistently.


# Vulnerability Lifecycle

```text
Software Released
        │
        ▼
Vulnerability Exists
        │
        ▼
Researcher or Attacker Discovers It
        │
        ▼
CVE Assigned
        │
        ▼
Vendor Releases Patch
        │
        ▼
Organisations Install Updates
```


# Famous Vulnerabilities

| Vulnerability  | Year                          | Description                                                   |
| -------------- | ----------------------------- | ------------------------------------------------------------- |
| Shellshock     | 2014 (bug existed since 1992) | Bash vulnerability allowing remote command execution          |
| EternalBlue    | 2017                          | Windows SMB vulnerability used by WannaCry ransomware         |
| PrintNightmare | 2021                          | Windows Print Spooler privilege escalation vulnerability      |
| Follina        | 2022                          | Microsoft Office vulnerability allowing remote code execution |


# Responding to Vulnerabilities

The primary solution to a vulnerability is **patching**.

## Common Responses

### Apply Security Patches

Install vendor updates as soon as practical.

### Restrict Access

Limit access to trusted IP addresses until systems are patched.

### Temporary Vendor Mitigations

Follow vendor guidance to reduce risk before a patch is available.

### IPS (Intrusion Prevention System)

Blocks known exploit attempts before they reach the target.


### WAF (Web Application Firewall)

Protects web applications by filtering malicious HTTP/HTTPS traffic.


# 3. Supply Chain Attacks

## Definition

A **Supply Chain Attack** occurs when attackers compromise trusted software, libraries, or service providers to reach many victims.

Instead of attacking each company individually, attackers compromise a shared supplier.


## Examples

* SolarWinds
* 3CX
* Compromised software libraries
* Malicious software updates

# Supply Chain Attack Flow

```text
Software Vendor
        │
        ▼
Vendor Compromised
        │
        ▼
Malicious Update Released
        │
        ▼
Thousands of Customers Install Update
        │
        ▼
Mass Compromise
```

# Why Supply Chain Attacks Are Dangerous

* Trusted software becomes malicious.
* Many organisations are affected simultaneously.
* Victims may not realise the software has been compromised.

# Misconfiguration

## Definition

A **misconfiguration** is an insecure system setup caused by incorrect configuration rather than a software bug.

The software works correctly—the problem is how it has been configured.


# Common Examples

### Weak Passwords

```
admin
123456
password
1111
```

### Internet-Exposed Database

A sensitive database is accidentally made accessible from the internet.


### Open Cloud Storage

Cloud storage buckets are left publicly accessible.


### Excessive Permissions

Users or services receive more privileges than necessary.


### Default Settings

Default usernames and passwords remain unchanged after installation.


# Vulnerability vs Misconfiguration

| Vulnerability                       | Misconfiguration                               |
| ----------------------------------- | ---------------------------------------------- |
| Software flaw or bug                | Incorrect or insecure setup                    |
| Requires a vendor fix (patch)       | Requires configuration changes                 |
| Created during software development | Introduced during deployment or administration |


# Responding to Misconfigurations

## 1. Penetration Testing

Ethical hackers simulate attacks to identify security weaknesses before malicious actors do.


## 2. Vulnerability Scanning

Automated tools detect issues such as:

* Missing patches
* Weak passwords
* Outdated software
* Known vulnerabilities

## 3. Configuration Audits

Review systems against recognised security best practices.

Example:

* CIS Benchmarks
* Internal security standards

---
