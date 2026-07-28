# Humans: The Weakest Link

# Why Are Humans the Weakest Link?

Attackers often **don't attack computers first—they attack people.**

Humans have access to:

* Email accounts
* Company systems
* VPNs
* Databases
* Cloud services
* Sensitive documents

Instead of hacking a firewall, an attacker may simply trick an employee into giving access.

> **Key Idea:** It is often easier to **trick a person than hack a system.**

# Why Do Attackers Target Humans?

Because people can unknowingly give attackers access.

## Examples

| Target              | Attacker's Goal                      |
| ------------------- | ------------------------------------ |
| HR Manager          | Steal employee records               |
| Finance Employee    | Steal money or banking information   |
| IT Administrator    | Gain access to the corporate network |
| Government Employee | Obtain confidential information      |

# Social Engineering

## Definition

**Social Engineering** is the manipulation of people into revealing confidential information or performing actions that help an attacker.

Instead of exploiting software vulnerabilities, attackers exploit **human psychology**.

## How Social Engineering Works

A successful social engineering attack usually has two characteristics.

### 1. Trustworthy

The attacker pretends to be someone legitimate.

Examples:

* Company IT Support
* CEO
* Bank
* Microsoft
* Google
* Delivery company

### 2. Emotional

The attacker creates emotions that cause victims to act quickly.

Common emotions:

* Fear
* Urgency
* Curiosity
* Excitement
* Greed

Example:

> "Your account has been hacked! Click here immediately!"

The victim reacts before thinking.


# Common Social Engineering Attacks

# 1. Phishing

## Definition

A **phishing attack** is a fraudulent email, message, or website designed to steal information.

## Goal

Steal:

* Passwords
* Credit card details
* Banking information
* MFA codes
* Personal information

## Indicators of Phishing

* Suspicious sender
* Urgent language
* Unexpected attachments
* Fake login pages
* Spelling mistakes
* Unknown links

# 2. Malware Downloads

## Definition

Attackers trick users into installing malicious software.


## Common Techniques

### Fake Software

Example:

A fake browser installer that secretly installs malware.


### Fake CAPTCHA

The website asks the user to:

> "Verify you are human"

Instead of clicking a checkbox, it instructs the user to run malicious commands or download malware.

### SEO Poisoning

Attackers create fake websites that appear at the top of search engine results.

Victims download malware instead of legitimate software.

### Malicious QR Codes

QR codes redirect users to:

* Fake websites
* Malware downloads
* Credential theft pages

# 3. Deepfakes

## Definition

A **deepfake** uses AI to create fake audio or video that appears to come from a trusted person.

## Example

A finance employee receives a video call from someone who looks and sounds like their CEO.

The "CEO" urgently requests a large money transfer.

The employee believes it is genuine and sends the money.

## Goal

* Financial fraud
* Identity impersonation
* Data theft
* Credential theft

# 4. Impersonation

## Definition

The attacker pretends to be another person.

Examples:

* IT Support
* CEO
* Police
* Bank Employee
* Vendor
* Colleague

## Example

Attacker calls an employee.

Claims:

> "I'm from IT. We need your password to fix a problem."

Victim shares credentials.

Attacker gains access.

# 5. Other Human-Based Attacks

Examples include:

### USB Drop Attack

A malicious USB drive is left where employees are likely to find it.

An employee plugs it into a company computer, infecting the system.

### Insider Threat

A trusted employee intentionally or accidentally causes harm.

Examples:

* Data theft
* Sharing passwords
* Leaking confidential information

### Fake Job Offers

Attackers send fake recruitment emails containing:

* Malware
* Credential theft links
* Malicious attachments

# SOC's Role

The SOC focuses on **detecting and investigating attacks that bypass preventive controls.**

SOC analysts:

* Monitor alerts
* Investigate suspicious activity
* Identify compromised accounts
* Respond to incidents
* Escalate serious cases

# Detection vs Mitigation

These are two key concepts in cyber security.

| Detection                                                   | Mitigation                                               |
| ----------------------------------------------------------- | -------------------------------------------------------- |
| Finds attacks that have already occurred or are in progress | Prevents or reduces the likelihood and impact of attacks |
| Performed by SOC analysts                                   | Implemented through security controls and user education |
| Focuses on identifying malicious activity                   | Focuses on stopping attacks before they succeed          |

# Examples

| Attack             | Mitigation                        | Detection                                                |
| ------------------ | --------------------------------- | -------------------------------------------------------- |
| Phishing email     | Anti-phishing email filter        | SOC investigates suspicious email alerts                 |
| Malware            | Antivirus or EDR blocks execution | SOC investigates malware detection alerts                |
| Deepfake request   | Verify identity before acting     | SOC investigates unusual account or transaction activity |
| Stolen credentials | Multi-factor authentication (MFA) | SOC detects suspicious login attempts                    |


# Mitigation Techniques

---

## 1. Anti-Phishing Solution

Blocks phishing emails before they reach employees.

Benefits:

* Reduces phishing attempts
* Lowers SOC workload
* Protects users automatically

## 2. Antivirus / EDR

Protects computers from malware.

### Antivirus

Detects and removes known malicious software.

### EDR (Endpoint Detection and Response)

Provides advanced capabilities to:

* Detect suspicious behaviour
* Investigate incidents
* Contain compromised endpoints
* Support incident response

## 3. Trust but Verify

Employees should always verify unusual requests.

Examples:

* Call the person back using a trusted number.
* Confirm requests through another communication channel.
* Verify unexpected payment or password reset requests.

Never rely solely on email, phone calls, or video if something seems suspicious.

## 4. Security Awareness Training

Employees learn to recognise attacks.

Training topics include:

* Phishing
* Password security
* Safe browsing
* Social engineering
* Data protection

Many organisations also run **phishing simulations** to test employee awareness.

# Real-World Attack Flow

```text
Attacker
    │
    ▼
Social Engineering Attack
    │
    ▼
Employee
    │
    ├───────────────┐
    │               │
Recognises Attack   Falls for Attack
    │               │
Attack Prevented    Account/System Compromised
                    │
                    ▼
            SOC Detects the Incident
                    │
                    ▼
         Investigation and Response
```

