# PayPal Phishing Email Analysis 

Let's analyze the email like a **SOC Level 1 Security Analyst**.

---

##  Step 1: Analyze the Subject Line

**Subject:**

```text
Your Receipt for Payment to Amazing Stuff
```

### Observation:

* The email claims that a payment has already been made.
* Most users would panic after seeing an unexpected transaction.
* The attacker wants the victim to react immediately.

### Red Flag:

⚠️ **Creates urgency and fear.**

---

##  Step 2: Analyze the Sender's Address

### What the user sees:

```text
service@paypal.com
```

### The actual email address:

```text
...@sultanbogor.com
```

### Observation:

The display name says **PayPal**, but the real domain is **sultanbogor.com**.

### Red Flag:

⚠️ **Spoofed email address.**

```text
Displayed sender → service@paypal.com

Actual sender → ...@sultanbogor.com
```

---

##  Step 3: Analyze the Recipient's Address

The recipient's address is unusual.

```text
008812034377940108id@mail.info.cnsmr.sg.yahoo.net
```

### Observation:

This doesn't look like a normal Yahoo email address.

### Red Flag:

⚠️ **Unusual recipient address.**

---

#  Step 4: Analyze the Email Body

The email says:

```text
You sent a payment of $120.00 USD to Amazing Stuff.
```

### Purchase Details:

| Item                    | Quantity | Amount |
| ----------------------- | -------- | ------ |
| Amazing Stuff Gift Card | 4        | $120   |

---

### Observation:

The victim never purchased these gift cards.

The attacker hopes the victim will become worried and try to cancel the order.

### Red Flag:

⚠️ **Fake transaction notification.**

---

#  Step 5: Analyze the Greeting

```text
Hello Customer,
```

### Observation:

The email doesn't use the recipient's real name.

### Red Flag:

⚠️ **Generic greeting.**

Legitimate companies usually personalize their emails.

---

#  Step 6: Analyze the Branding

The email contains:

```text
✓ PayPal logo

✓ PayPal layout

✓ PayPal colors

✓ PayPal formatting
```

### Observation:

The attacker copied PayPal's appearance.

### Red Flag:

⚠️ **Brand impersonation.**

---

#  Step 7: Analyze the Button

The most dangerous element is:

```text
[ Cancel the order ]
```

### Observation:

The attacker expects the victim to click the button to cancel the payment.

### Red Flag:

⚠️ **The button contains a hidden link.**

---

#  Step 8: Investigate the URL

The button redirects to a **shortened URL**.

Example:

```text
bit.ly/abc123
```

### Why is this dangerous?

The final destination is hidden.

```text
Shortened URL
        │
        ▼
Unknown Website
        │
        ▼
Credential Theft Page
```

### Red Flag:

⚠️ **URL obfuscation.**

---

#  Step 9: Defang the URL

Never click a suspicious link.

Instead of:

```text
http://malicious-site.com
```

Use:

```text
hxxp[://]malicious-site[.]com
```

This prevents accidental clicks.

---------------------



#  Track Your Package

In this phishing email, the attacker pretends to be a **shipping company**.

The goal is to convince the victim to click a fake package-tracking link.

---

# Phishing Techniques Used

| Technique             | Purpose                                  |
| --------------------- | ---------------------------------------- |
| Spoofed email address | Pretend to be a trusted shipping company |
| Pixel tracking        | Detect when the email is opened          |
| Link manipulation     | Hide the actual destination of the link  |

---

# Step 1: Analyze the Subject Line

**Subject:**

```text
Track your package: #LZ8942357486EN
```

### Observation

The attacker includes a tracking number to make the email look legitimate.

### Goal

Create curiosity and urgency.

The victim thinks:

```text
I have a package.

I should check it immediately.
```

### Red Flag

Unexpected package notification.

---

# Step 2: Analyze the Sender

**Display name:**

```text
Distribution Center
```

**Actual email address:**

```text
contact@beginpro.club
```

### Observation

A legitimate shipping company would normally use a domain such as:

```text
ups.com
fedex.com
dhl.com
usps.com
```

Instead, the email comes from:

```text
beginpro.club
```

### Red Flag

Spoofed sender address.

---

# Step 3: Analyze the Hyperlink

**Visible text:**

```text
Track your package: #LZ8942357486EN
```

### Observation

The tracking number itself is a hyperlink.

The victim believes they are opening a shipment-tracking page.

However, the actual destination is hidden.

### Red Flag

Link manipulation.

---

# Why Did Yahoo Block the Images?

Yahoo blocked the images because the email contains **tracking pixels**.

---

# What Is a Tracking Pixel?

A tracking pixel is a very small, invisible image embedded inside an email.

When the email is opened:

```text
Victim opens the email
          │
          ▼
Hidden image loads
          │
          ▼
The attacker's server is contacted
          │
          ▼
The attacker knows the email was opened
```

---

# Source Code Analysis

The HTML source reveals the actual link:

```html
<a href="http://devret.xyz/...">
Track your package
</a>
```

---

## Displayed Text

```text
Track your package: #LZ8942357486EN
```

---

## Actual Destination

```text
http://devret.xyz/...
```

The displayed text and the actual URL are different.

---

# Tracking Pixel Found

```html
<img src="http://devret.xyz/Creatives/Tracking.png">
```

### Observation

The image is loaded from the attacker's server.

### Purpose

The attacker can determine:

* Whether the email was opened
* When it was opened
* Which recipient opened it

---

# Another Suspicious Element

```html
<img style="width:0px;height:0px;display:none;">
```

### Observation

The image is invisible.

### Purpose

Hide the tracking pixel from the victim.

---

# Indicators of Compromise (IOCs)

| IOC                | Evidence                  |
| ------------------ | ------------------------- |
| Suspicious domain  | beginpro.club             |
| Suspicious URL     | devret.xyz                |
| Tracking pixel     | Tracking.png              |
| Spoofed sender     | Distribution Center       |
| Hidden destination | Tracking number hyperlink |

---

# Attack Flow

```text
Fake shipping email
          │
          ▼
Victim sees a package notification
          │
          ▼
Victim clicks the tracking number
          │
          ▼
Victim is redirected to a malicious website
          │
          ▼
Tracking pixels notify the attacker
```

----------------------
#  Download Document Here 

In this attack, the attacker uses **multiple fake websites** to steal the victim's login credentials.

Instead of sending the victim directly to the fake login page, the attacker creates a **redirection chain**.

The goal is to make the victim trust the websites and enter their email password.

---

# Phishing Techniques Used

| Technique             | Purpose                                        |
| --------------------- | ---------------------------------------------- |
| Artificial urgency    | Force the victim to act quickly                |
| Brand impersonation   | Increase trust by copying well-known companies |
| Link redirection      | Hide the final malicious website               |
| Credential harvesting | Steal usernames and passwords                  |

---

# Step 1: Analyze the Email

## Send Date

```text
Thursday, July 15, 2021
```

The email was sent on July 15.

---

## Expiration Date

```text
Expires July 15, 2021
```

The document expires **on the same day**.

### Why is this suspicious?

The attacker wants the victim to think:

```text
I don't have much time.

I need to open this document immediately.
```

This creates **artificial urgency**.

---

## Download Button

```text
Download Document Here
```

The attacker wants the victim to click this button.

This is the first step of the attack.

---

# Step 2: First Redirection (Fake OneDrive)

After clicking the button, the victim is redirected to a website that looks like **OneDrive**.

```text
Email
   │
   ▼
Fake OneDrive Page
```

The page displays a PDF document icon and a **Get Document** button.

---

## Red Flag: Suspicious URL

The website address does not belong to Microsoft.

The page only **looks** like OneDrive.

```text
Real website:
onedrive.live.com

Fake website:
Unknown or suspicious domain
```

---

# Step 3: Second Redirection (Fake Adobe)

After clicking the next button, the victim is redirected again.

```text
Email
   │
   ▼
Fake OneDrive
   │
   ▼
Fake Adobe Page
```

The attacker now uses **Adobe branding**.

---

## Red Flag: Nonsensical Instructions

The page says:

```text
To view the document,
please enter your valid email credentials.
```

This instruction doesn't make sense.

Normally, Adobe does **not** ask for your email password to open a PDF document.

---

## Red Flag: Multiple Sign-In Options

The page offers several login choices:

```text
Sign in with Outlook

Sign in with Office 365

Sign in with Other Mail
```

The attacker wants to steal credentials from as many email providers as possible.

---

# Step 4: Fake Outlook Login Page

The victim selects **Outlook**.

```text
Email
   │
   ▼
Fake OneDrive
   │
   ▼
Fake Adobe
   │
   ▼
Fake Outlook Login
```

The page asks for:

```text
Email address

Password
```

---

# Step 5: Credential Harvesting

The victim enters:

```text
Email: user@example.com

Password: MyPassword123
```

---

Instead of sending the information to Microsoft, the website sends it directly to the attacker.

```text
Victim enters credentials
              │
              ▼
Credentials sent
              │
              ▼
Attacker's server
```

---

# Step 6: Fake Error Message

After entering the password, the victim sees:

```text
Invalid Credentials
```

---

## Why does this happen?

Because the website isn't actually connected to Outlook.

The attacker only wants to collect the credentials.

After stealing them, the website displays a fake error message.

---

# Why Use Multiple Redirections?

## Hide the Malicious Website

```text
Email
   │
   ▼
Fake OneDrive
   │
   ▼
Fake Adobe
   │
   ▼
Fake Outlook Login
```

---

## Bypass Security Filters

A long chain of redirects makes it more difficult for:

* Email filters
* Antivirus software
* Security tools

to detect the attack.

---

# Indicators of Compromise (IOCs)

| IOC                     | Evidence                                            |
| ----------------------- | --------------------------------------------------- |
| Urgency                 | Document expires on the same day                    |
| Brand impersonation     | OneDrive                                            |
| Brand impersonation     | Adobe                                               |
| Brand impersonation     | Outlook                                             |
| Redirection             | Multiple websites                                   |
| Credential harvesting   | Fake login page                                     |
| Suspicious instructions | "Enter your email credentials to view the document" |

---

# Attack Flow

```text
Phishing Email
        │
        ▼
Download Document Here
        │
        ▼
Fake OneDrive Page
        │
        ▼
Get Document
        │
        ▼
Fake Adobe Page
        │
        ▼
Sign in with Outlook
        │
        ▼
Fake Login Page
        │
        ▼
Victim enters credentials
        │
        ▼
Attacker steals credentials
```

----------------

#  Your Account Is on Hold 

In this phishing email, the attacker pretends to be **Netflix Billing**.

Unlike the previous examples that used malicious links, this attack uses a **PDF attachment** to hide the malicious URL.

The goal is to make the victim open the attachment and update their billing information.

---

# Phishing Techniques Used

| Technique              | Purpose                                  |
| ---------------------- | ---------------------------------------- |
| Spoofed email address  | Pretend to be Netflix                    |
| Sense of urgency       | Pressure the victim to act immediately   |
| Brand impersonation    | Copy Netflix's appearance                |
| Poor grammar and typos | Mistakes in the email content            |
| Malicious attachment   | Hide the malicious URL inside a PDF file |

---

# Step 1: Analyze the Subject Line

**Subject:**

```text
Netflix ID Suspended
```

---

## Observation

The email claims that the user's Netflix account has been suspended.

The attacker wants the victim to panic.

---

## Why Is This Suspicious?

The victim thinks:

```text
My Netflix account has been suspended.

I need to fix this immediately.
```

This creates a **sense of urgency**.

---

# Step 2: Analyze the Sender

**Display name:**

```text
Netflix Billing
```

**Actual email address:**

```text
z99@musacomb.online
```

---

## Observation

The display name says **Netflix Billing**, but the actual domain is:

```text
musacomb.online
```

---

## Red Flag

Legitimate Netflix emails come from Netflix-owned domains.

```text
Expected:

netflix.com
```

```text
Found:

musacomb.online
```

This is a **spoofed email address**.

---

# Step 3: Analyze the Branding

The email contains:

```text
Netflix logo

Netflix colors

Netflix formatting
```

---

## Observation

The attacker copied Netflix's appearance to make the email look authentic.

This is called **brand impersonation**.

---

# Step 4: Analyze the Email Body

The email says:

```text
Please update your payment details.
```

---

## Observation

The attacker claims there is a problem with the victim's billing information.

The goal is to convince the victim to update their account.

---

# Step 5: Analyze the PDF Attachment

Unlike previous attacks, this email doesn't include a direct hyperlink.

Instead, it includes a **PDF attachment**.

```text
Payment-up.pdf
```

---

## Why Use a PDF Attachment?

Attackers often hide malicious links inside documents because:

* Users trust PDF files.
* Some email filters focus on hyperlinks instead of attachments.
* The malicious URL is hidden inside the document.

---

# Step 6: Analyze the PDF Content

Inside the PDF, there is a button:

```text
Update Payment Account
```

---

## Observation

The button contains an embedded link.

However, the destination does **not** belong to Netflix.

---

## Red Flag

The PDF redirects the victim to a website that isn't associated with Netflix.

---

# Step 7: Analyze the Phone Number

The PDF contains an unusual phone number format.

---

## Why Is This Suspicious?

Legitimate companies usually display their contact information consistently.

An unusual phone number format is another warning sign.

---

# Step 8: False Trust Through a Legitimate Domain

The PDF contains links to:

```text
Netflix Help Center
```

and

```text
Contact Us
```

---

## Observation

Attackers sometimes include **legitimate links** together with malicious links.

---

## Why?

To build trust.

The victim thinks:

```text
The Help Center link is legitimate.

This email must be real.
```

---

# Attack Flow

```text
Fake Netflix Email
          │
          ▼
Victim sees "Account Suspended"
          │
          ▼
Victim opens the PDF attachment
          │
          ▼
Victim clicks "Update Payment Account"
          │
          ▼
Victim is redirected to a malicious website
          │
          ▼
Billing information is stolen
```

---

# Indicators of Compromise (IOCs)

| IOC                  | Evidence                |
| -------------------- | ----------------------- |
| Suspicious sender    | musacomb.online         |
| Urgent subject       | Netflix ID Suspended    |
| Brand impersonation  | Netflix logo and design |
| Attachment           | Payment-up.pdf          |
| Suspicious button    | Update Payment Account  |
| Poor grammar         | Multiple writing errors |
| Unusual phone number | Incorrect format        |

---

# Red Flags

```text
Urgent message

Spoofed sender address

Fake Netflix branding

PDF attachment

Hidden malicious URL

Poor grammar

Unusual phone number
```

-----------------

#  Your Recent Purchase 

In this phishing email, the attacker pretends to be **Apple Support**.

Unlike the previous examples, the **email body is completely blank**. The attacker hides everything inside a **malicious attachment**.

The goal is to trick the victim into opening the file and clicking the hidden link.

---

# Phishing Techniques Used

| Technique              | Purpose                              |
| ---------------------- | ------------------------------------ |
| Spoofed email address  | Pretend to be Apple Support          |
| BCC                    | Hide the real recipient list         |
| Urgency                | Force the victim to act quickly      |
| Poor grammar and typos | Mistakes in the email                |
| Malicious attachment   | Hide the phishing link inside a file |

---

# Step 1: Analyze the Subject Line

**Subject:**

```text
Re: Action Required - Your recent purchase
"Double Jackpot Slots Las Vegas" on the App Store
```

---

## Observation

The email claims that the user recently purchased an application.

The attacker wants the victim to think:

```text
I didn't buy this.

I need to fix this immediately.
```

This creates a **sense of urgency**.

---

# Step 2: Analyze the Sender

**Display name:**

```text
Apple Support
```

**Actual email address:**

```text
donoreply-storemails...@sumpremed.com
```

---

## Observation

The display name says **Apple Support**, but the email domain is:

```text
sumpremed.com
```

---

## Red Flag

Legitimate Apple emails come from Apple-owned domains.

```text
Expected:

apple.com
```

```text
Found:

sumpremed.com
```

This is a **spoofed email address**.

---

# Step 3: Analyze the Recipient

The email uses:

```text
BCC
```

---

## What is BCC?

**BCC (Blind Carbon Copy)** hides recipients from one another.

---

## Why is this suspicious?

The victim was **not directly addressed**.

The attacker probably sent the same email to many users.

---

# Step 4: Analyze the Email Body

The email body is **completely blank**.

---

## Why is this suspicious?

Legitimate companies usually provide:

* Purchase details
* Contact information
* Billing information
* Customer support information

This email provides **nothing**.

The only content is an attachment.

---

# Step 5: Analyze the Attachment

The attachment is:

```text
Double Jack... .dot
```

---

## What is a .dot file?

A **.dot** file is a **Microsoft Word template**.

---

## Why is this suspicious?

Receipts are usually sent as:

```text
PDF (.pdf)

Web pages

Email messages
```

A **Word template** is an unusual format for a purchase receipt.

---

# Step 6: Open the Attachment

The attachment contains a fake purchase receipt.

The document displays:

```text
Apple logo

Order ID

Product details

Billing information
```

The attacker wants the document to look legitimate.

---

# Step 7: Hidden Redirect Link

The large image inside the document contains a hidden hyperlink.

When the victim clicks the image, they are redirected to another website.

---

## Suspicious URL

The URL contains familiar words such as:

```text
apps

ios
```

---

## Why is this suspicious?

Attackers often include trusted words inside URLs.

Example:

```text
https://apps.ios.games.example.com
```

The URL looks legitimate at first.

However, the address is:

* Very long
* Difficult to read
* Unnecessarily complex

These are common signs of a phishing URL.

---

# Attack Flow

```text
Fake Apple Email
          │
          ▼
Urgent Purchase Notification
          │
          ▼
Victim opens the attachment
          │
          ▼
Victim clicks the large image
          │
          ▼
Hidden redirect link
          │
          ▼
Malicious website
```

---

# Indicators of Compromise (IOCs)

| IOC                 | Evidence                      |
| ------------------- | ----------------------------- |
| Spoofed sender      | Apple Support → sumpremed.com |
| Urgency             | Action Required               |
| BCC                 | Email was not sent directly   |
| Empty email body    | No message content            |
| Unusual attachment  | .dot file                     |
| Long URL            | Hidden redirection            |
| Brand impersonation | Apple branding                |

---

# Red Flags

```text
Fake Apple Support

Urgent message

BCC recipient

Blank email body

Unusual .dot attachment

Long redirect URL

Hidden hyperlink
```

---------------------

# Scheduled Shipment

In this phishing email, the attacker pretends to be **DHL Express**.

The email looks like a normal shipping notification, but the real attack is hidden inside an **Excel attachment**.

The goal is to trick the victim into opening the attachment and running a **malicious executable file**.

---

# Phishing Techniques Used

| Technique             | Purpose                               |
| --------------------- | ------------------------------------- |
| Spoofed email address | Pretend to be DHL Express             |
| Brand impersonation   | Copy DHL's appearance                 |
| Malicious attachment  | Deliver malware through an Excel file |

---

# Step 1: Analyze the Subject Line

**Subject:**

```text
DHL Express Courier Shipping Notice CBJ200620039539
```

---

## Observation

The email claims that DHL has scheduled a package shipment.

The attacker wants the victim to think:

```text
I have a package.

I should check the shipment details.
```

---

## Why is this suspicious?

Many users immediately open shipping notifications without verifying them.

---

# Step 2: Analyze the Sender

**Display name:**

```text
DHL Express
```

**Actual email address:**

```text
info@glamcarcompany.de
```

---

## Red Flag

The display name says **DHL Express**, but the domain belongs to another company.

```text
Expected:

dhl.com
```

```text
Found:

glamcarcompany.de
```

This is a **spoofed email address**.

---

# Step 3: Analyze the Email Body

The email contains:

```text
DHL logo

DHL colors

Shipping information
```

---

## Observation

The attacker copied DHL's appearance to make the email look legitimate.

This is called **brand impersonation**.

---

# Step 4: Analyze the Attachment

The attachment is:

```text
CBJ2006200...xlsx
```

---

## What is an .xlsx file?

An **.xlsx** file is a Microsoft Excel spreadsheet.

---

## Why is this suspicious?

When the file is opened, several inconsistencies appear.

---

### Inconsistency 1: German Domain

The sender uses a German domain:

```text
glamcarcompany.de
```

---

### Inconsistency 2: Indian Address

The invoice is addressed to:

```text
Waranagar, Kolhapur, India
```

---

### Inconsistency 3: Chinese Content

The spreadsheet contains **Mandarin (Chinese)** text.

---

## Why is this suspicious?

A legitimate DHL document would not normally contain information from three different regions.

```text
Germany
     +
India
     +
China
```

These conflicting details are a major warning sign.

---

# Step 5: Analyze the Hyperlink

The spreadsheet contains a message:

```text
TO TRANSLATE LANGUAGE
KINDLY CLICK ENABLE EDITING ABOVE
```

---

## Observation

The spreadsheet contains a hidden hyperlink.

The attacker wants the victim to click it.

---

# Step 6: Malicious Payload Download

After the link is clicked, the spreadsheet attempts to download:

```text
regasms.exe
```

---

## What is an .exe file?

An **.exe** file is an executable program.

Executable files can:

* Install software
* Modify files
* Run commands

---

## Red Flag

Shipping invoices should **never** download executable programs.

---

# Step 7: Malware Execution

```text
Victim opens the spreadsheet
             │
             ▼
Victim clicks the hyperlink
             │
             ▼
regasms.exe is downloaded
             │
             ▼
The executable runs
```

---

In this example, the execution fails and displays an error message.

However, the attacker clearly intended to run code on the victim's computer.

---

# What Could the Malware Do?

## Establish Persistence

```text
Install a backdoor

Create a scheduled task

Run automatically after every reboot
```

---

## Steal Information

```text
Passwords

Documents

Browser data

Credentials
```

---

## Deploy Ransomware

```text
Encrypt files

Lock the computer

Demand payment
```

---

# Attack Flow

```text
Fake DHL Email
         │
         ▼
Victim opens the Excel attachment
         │
         ▼
Victim clicks the hyperlink
         │
         ▼
regasms.exe is downloaded
         │
         ▼
Malware executes
         │
         ▼
Persistence + Data Theft + Ransomware
```

---

# Indicators of Compromise (IOCs)

| IOC                        | Evidence                                                |
| -------------------------- | ------------------------------------------------------- |
| Spoofed sender             | [info@glamcarcompany.de](mailto:info@glamcarcompany.de) |
| Brand impersonation        | DHL logo                                                |
| Malicious attachment       | .xlsx file                                              |
| Geographic inconsistencies | Germany + India + China                                 |
| Hidden hyperlink           | Enable Editing message                                  |
| Malware download           | regasms.exe                                             |
| Code execution             | Executable running                                      |

---

# Red Flags

```text
Fake DHL email

Unexpected Excel attachment

Mixed geographic information

Chinese text

Hidden hyperlink

Executable file download

Malware execution
```

----------------
