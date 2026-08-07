# Splunk Architecture 

# What is Splunk Architecture?

Splunk has **3 main components** that work together to collect, store, and search logs.

```text
             Splunk Architecture

Log Source
     │
     ▼
Forwarder
     │
     ▼
Indexer
     │
     ▼
Search Head
     │
     ▼
SOC Analyst
```

---

# 1. Splunk Forwarder

## What is Splunk Forwarder?

**Splunk Forwarder** is a **lightweight agent (small software installed on a device)** that collects logs from endpoints and sends them to the Splunk Indexer.

Think of it as a **delivery person** who collects logs and delivers them to Splunk.

---

## Main Functions

* Collect logs from endpoints.
* Send logs to the Splunk Indexer.
* Uses very little CPU and memory.
* Runs continuously in the background.

---

## Common Log Sources

### Windows Machine

Collects:

* Windows Event Logs
* PowerShell Logs
* Sysmon Logs

---

### Linux Server

Collects:

* System Logs
* Authentication Logs
* Application Logs

---

### Web Server

Collects:

* Website traffic
* Access Logs
* Error Logs

---

### Database Server

Collects:

* Database connections
* Queries
* Errors
* Login events

---

## Example

A Windows computer creates an Event Log.

↓

Splunk Forwarder collects it.

↓

Sends it to the Indexer.

---

# 2. Splunk Indexer

## What is Splunk Indexer?

The **Indexer** is the **heart (main processing component)** of Splunk.

It receives logs from Forwarders and prepares them for searching.

---

## Main Functions

* Receives logs.
* Parses logs (breaks raw logs into meaningful parts).
* Normalizes logs (converts logs into a common format).
* Stores logs.
* Indexes logs for fast searching.

---

## What is Parsing?

**Parsing (breaking raw data into readable fields).**

Example:

Raw Log:

```text
User=John IP=192.168.1.20 Login=Success
```

After Parsing:

| Field | Value        |
| ----- | ------------ |
| User  | John         |
| IP    | 192.168.1.20 |
| Login | Success      |

---

## What is Normalization?

**Normalization (converting logs from different sources into a common format).**

Example:

Windows Log:

```text
AccountName=John
```

Linux Log:

```text
User=John
```

After normalization:

```text
Username=John
```

Now all logs are easier to search.

---

## What is Indexing?

**Indexing (organizing data so searches are much faster).**

Instead of reading every log one by one,

Splunk creates an index so it can quickly find matching events.

---

# Example

Forwarder sends:

```text
User Login

User=John

IP=192.168.1.20
```

Indexer:

* Parses it.
* Normalizes it.
* Stores it.

Now it's ready for searching.

---

# 3. Search Head

## What is Search Head?

The **Search Head** is the interface (dashboard) where SOC analysts search, investigate, and visualize logs.

This is the part analysts use every day.

---

## Main Functions

* Search logs.
* Investigate alerts.
* View events.
* Create reports.
* Create dashboards.
* Build visualizations.

---

# Search Using SPL

Splunk uses:

**SPL (Search Processing Language – Splunk's query language used to search logs).**

Example:

```spl
index=windows
```

Searches Windows logs.

---

Another example:

```spl
index=windows EventCode=4625
```

Searches failed Windows logins.

---

# Search Flow

```text
SOC Analyst

↓

Runs SPL Query

↓

Search Head

↓

Indexer

↓

Matching Logs Returned

↓

Displayed to Analyst
```

---

# Visualization

Search Head can convert search results into graphs.

Examples:

* Pie Chart
* Bar Chart
* Column Chart
* Line Chart
* Tables

This makes large amounts of log data easier to understand.

---

# Complete Splunk Workflow

```text
Windows PC
Linux Server
Web Server
Database
        │
        ▼
Splunk Forwarder
(Collect Logs)
        │
        ▼
Splunk Indexer
(Parse → Normalize → Index → Store)
        │
        ▼
Splunk Search Head
(Search & Reporting)
        │
        ▼
SOC Analyst
```

---

# Easy Way to Remember

### Forwarder

 **Collects and Sends Logs**

Think:

**Collector**

---

### Indexer

 **Processes and Stores Logs**

Think:

**Library**

---

### Search Head

 **Searches and Displays Logs**

Think:

**Google Search for Logs**

---

# Summary Table

| Component       | Purpose                                               | Simple Meaning          |
| --------------- | ----------------------------------------------------- | ----------------------- |
| **Forwarder**   | Collects logs from endpoints and sends them to Splunk | **Collector**           |
| **Indexer**     | Parses, normalizes, indexes, and stores logs          | **Processor & Storage** |
| **Search Head** | Searches logs and displays results                    | **Search Interface**    |

-------------------
# Splunk Home Screen 

# Splunk Home Screen

When you log in to Splunk, the **Home Screen (main page after login)** appears.

It has **4 main sections**:

```text
Splunk Home Screen
│
├── Splunk Bar
├── Apps Panel
├── Explore Splunk
└── Home Dashboard
```

---

# 1. Splunk Bar

## What is the Splunk Bar?

The **Splunk Bar (top navigation bar)** is located at the top of the page.

It provides quick access to important options.

---

## Options in the Splunk Bar

### Messages

**Messages (system notifications and alerts)**

Used to:

* View system notifications.
* Check Splunk warnings.
* See important updates.

---

### Settings

**Settings (configuration options)**

Used to:

* Configure Splunk.
* Manage users.
* Create indexes.
* Configure data inputs.

---

### Activity

**Activity (search job status)**

Shows:

* Running searches.
* Completed searches.
* Background jobs.
* Search progress.

Useful for checking whether a search is still running.

---

### Help

**Help (documentation and tutorials)**

Provides:

* Splunk documentation.
* User guides.
* Tutorials.
* Learning resources.

---

### Find

**Find (search across Splunk apps and objects)**

Used to quickly locate:

* Dashboards.
* Reports.
* Saved searches.
* Apps.

---

### Apps Menu

You can also switch between installed Splunk Apps directly from the Splunk Bar.

Example:

```text
Search & Reporting

↓

Enterprise Security

↓

Other Apps
```

No need to use the Apps Panel.

---

# 2. Apps Panel

## What is the Apps Panel?

The **Apps Panel (list of installed Splunk applications)** displays all apps available in the Splunk instance.

---

## Default App

Every Splunk installation includes:

**Search & Reporting**

This is the app most SOC analysts use daily.

---

## Purpose

Allows users to:

* Open installed apps.
* Switch between apps.
* Access different Splunk features.

---

# 3. Explore Splunk

## What is Explore Splunk?

The **Explore Splunk** section provides quick shortcuts for common tasks.

---

## Common Options

### Add Data

Import new log sources into Splunk.

Examples:

* Windows Event Logs
* Sysmon Logs
* Web Logs
* Linux Logs

---

### Find Apps

Install additional Splunk Apps.

Examples:

* Cisco App
* Microsoft App
* AWS App

---

### Documentation

Open official Splunk documentation and learning resources.

Useful for:

* Learning SPL.
* Understanding features.
* Troubleshooting.

---

# 4. Home Dashboard

## What is the Home Dashboard?

The **Home Dashboard (main visualization area)** displays dashboards containing charts, graphs, and reports.

---

## By Default

A new Splunk installation may not show any dashboards.

You can:

* Select existing dashboards.
* Create your own dashboards.
* Customize the Home Screen.

---

## Dashboard Examples

Dashboards may display:

* Login Failures
* Malware Alerts
* Network Traffic
* Endpoint Activity
* User Activity

---

## Yours Tab

The **Yours** tab displays dashboards created by you.

Useful for:

* Personal dashboards.
* Custom reports.
* Saved visualizations.

---

# Splunk Navigation Flow

```text
Login
   │
   ▼
Splunk Home
   │
   ├── Splunk Bar
   │
   ├── Apps Panel
   │
   ├── Explore Splunk
   │
   └── Home Dashboard
```

----------------------------

# Adding Data to Splunk 


# What is Data Ingestion?

**Data Ingestion** is the process of **collecting and importing data (logs)** into Splunk for searching and analysis.

When data is added:

```text
Raw Logs
      │
      ▼
Splunk Processes Data
      │
      ▼
Converts into Events
      │
      ▼
Stores in an Index
      │
      ▼
Ready for Searching
```

---

# What is an Event?

An **Event** is a **single log entry** stored in Splunk.

### Example

Raw Log:

```json
{"UserName":"John","Source_IP":"192.168.1.10","Action":"Login"}
```

After importing, Splunk stores this as **one event**.

---

# Common Data Sources

Splunk can collect logs from many sources.

| Data Source     | Example                     |
| --------------- | --------------------------- |
| Windows Logs    | Event Viewer, Sysmon        |
| Linux Logs      | Syslog, Authentication Logs |
| Web Server Logs | Apache, Nginx, IIS          |
| Firewall Logs   | Cisco ASA, Palo Alto        |
| VPN Logs        | VPN Login & Connection Logs |
| Database Logs   | SQL Server, MySQL           |
| Cloud Logs      | AWS, Azure, GCP             |

---

# Example Used in This Lab

In this lab, we import:

**VPN Logs**

These logs contain information such as:

* Username
* Source IP
* Country
* Login Time
* VPN Connection Status

---

# Steps to Upload Data into Splunk

Splunk provides an **Upload Wizard**.

There are **5 steps**.

---

## Step 1 – Select Source

Choose the log file to upload.

Example:

```text
VPN_logs
```

This tells Splunk which file should be imported.

---

## Step 2 – Select Source Type

Choose the log format.

Example:

```text
JSON
```

Since **VPN_logs** is a **newline-delimited JSON (each line is a separate JSON object/event)**, Splunk automatically detects it.

---

## Step 3 – Input Settings

Configure where the logs will be stored.

### Index

**Index (database/location where Splunk stores logs).**

Example:

```text
VPN_Logs
```

---

### Host

**Host (device name associated with the logs).**

Example:

```text
VPN-Server
```

---

## Step 4 – Review

Review all settings before importing.

Check:

* File
* Source Type
* Index
* Host

If everything is correct,

Click **Next**.

---

## Step 5 – Done

Splunk imports the logs.

Now they are searchable.

---

# After Upload

Open:

```text
Search & Reporting
```

Set the **Time Range** to:

```text
All Time
```

Otherwise,

Splunk may not display imported logs.

---

# What is `spath`?

Sometimes JSON fields are not automatically extracted.

Use:

```spl
| spath
```

**spath (Search Path command that extracts fields from JSON or XML data).**

Example:

```spl
index=VPN_Logs
| spath
```

Now fields like:

* UserName
* Source_IP
* Country

become searchable.

---

# Useful SPL Queries

## 1. Count Total Logs

```spl
index=VPN_Logs
| stats count
```

### Meaning

Shows:

Total number of imported events.

---

## 2. Search for User "Maleena"

```spl
index=VPN_Logs
| spath
| search UserName="Maleena"
| stats count
```

### Meaning

Finds:

* User = Maleena

Returns:

Number of login events.

---

## 3. Find User by Source IP

```spl
index=VPN_Logs
| spath
| search Source_ip="107.14.182.38"
| stats values(UserName) as UserName count
```

### Meaning

Shows:

* Username
* Number of events

for the specified IP address.

---

## 4. Find Users Outside France

```spl
index=VPN_Logs
| spath
| search Source_Country!="France"
| stats count
```

### Meaning

Returns:

Total VPN connections from countries **other than France**.

---

## 5. Count Events from One IP

```spl
index=VPN_Logs
| spath
| search Source_ip="107.3.206.58"
| stats count
```

### Meaning

Shows:

How many VPN events originated from this IP address.

---

# Splunk Upload Workflow

```text
VPN Log File
      │
      ▼
Add Data
      │
      ▼
Select Source
      │
      ▼
Select Source Type (JSON)
      │
      ▼
Input Settings (Index)
      │
      ▼
Review
      │
      ▼
Upload Complete
      │
      ▼
Search & Reporting
      │
      ▼
Run SPL Queries
```

------------------

