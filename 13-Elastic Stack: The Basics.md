# Elastic Stack (ELK) – Simple Notes

## What is Elastic Stack?

**Elastic Stack (ELK)** is a collection of tools used to:

* Collect data
* Process logs
* Store data
* Search logs
* Analyze security events
* Create dashboards and visualizations

It is widely used by **SOC teams for log analysis and investigations**.

> **ELK = Elasticsearch + Logstash + Beats + Kibana**

---

# 1. Elasticsearch

**Elasticsearch (search and analytics engine)** is where the processed data is stored and searched.

### Main functions:

* Stores data.
* Searches data quickly.
* Analyzes data.
* Correlates (connects related events) data.
* Works with JSON-formatted documents.
* Provides a **REST API (way for applications to communicate with Elasticsearch using HTTP requests)**.

### Simple meaning:

 **Elasticsearch = Storage + Search**

---

# 2. Logstash

**Logstash (data processing engine)** collects data from different sources, processes it, and sends it to a destination.

It has **3 main parts**:

```text
Input → Filter → Output
```

### Input

**Input (where the data comes from)**

Examples:

* File
* Port
* Beats
* Application logs

---

### Filter

**Filter (processes and normalizes the data)**

It can:

* Parse logs.
* Remove unwanted information.
* Convert data into useful fields.
* Normalize different log formats.

Example:

```text
Raw Log
   ↓
Filter
   ↓
Username = Sai
IP = 10.0.0.5
Action = Login
```

---

### Output

**Output (where processed data is sent)**

Examples:

* Elasticsearch
* File
* Listening port

### Simple meaning:

⚙️ **Logstash = Process and Transform**

---

# 3. Beats

**Beats (lightweight data-shipping agents)** are installed on endpoints and collect specific types of data.

Think of Beats as **small collectors** that send data to Elasticsearch or Logstash.

---

## Examples

### Winlogbeat

Collects:

**Windows Event Logs**

---

### Packetbeat

Collects:

**Network traffic information**

---

Other Beats can collect different types of endpoint data.

### Simple meaning:

📥 **Beats = Collect and Send**

---

# 4. Kibana

**Kibana (web-based visualization and investigation tool)** works with Elasticsearch.

It allows analysts to:

* Search data.
* Investigate logs.
* Create dashboards.
* Create charts.
* View timelines.
* Visualize security events.

### Simple meaning:

📊 **Kibana = Search + Visualization**

---

# How ELK Components Work Together

The basic flow is:

```text
Endpoints
   │
   ▼
Beats
(Collect Data)
   │
   ▼
Logstash
(Process / Normalize)
   │
   ▼
Elasticsearch
(Store / Search)
   │
   ▼
Kibana
(Visualize / Investigate)
   │
   ▼
SOC Analyst
```

---

# Example: Windows Login Investigation

Imagine a user logs into a Windows computer.

### Step 1 – Winlogbeat

**Winlogbeat** collects the Windows Event Log.

```text
User: Sai
Event: Login
IP: 192.168.1.20
```

↓

### Step 2 – Logstash

Logstash processes and normalizes the log.

```text
Username = Sai
Event = Login
Source_IP = 192.168.1.20
```

↓

### Step 3 – Elasticsearch

Elasticsearch stores and indexes the data.

↓

### Step 4 – Kibana

The SOC analyst searches the event and creates visualizations.

↓

### Step 5 – Analyst

The analyst can determine whether the login is:

 Normal

or

 Suspicious

---------------

# Kibana Discover Tab – Simple Notes

## What is Kibana Discover?

**Discover** is the main workspace in **Kibana** where SOC analysts search, filter, and investigate logs stored in Elasticsearch.

Think of it as:

> **Kibana Discover = Search and Investigation Area for Logs**

As a SOC L1 analyst, you will spend a lot of time here investigating alerts and suspicious activity.

---

# Discover Tab – Main Components

```text
Kibana Discover
│
├── Logs
├── Fields Pane
├── Index Pattern
├── Search Bar
├── Time Filter
├── Timeline
├── Top Bar
└── Add Filter
```

---

## 1. Logs

**Logs (individual records/events)** are displayed in the main area.

Each row represents one event.

Example:

```text
User: John
Source IP: 10.10.10.5
Action: VPN Login
Country: India
```

You can open a log to see more details.

### Simple meaning:

**Logs = Actual events recorded by the system**

---

# 2. Fields Pane

The **Fields Pane (left-side list of available fields)** shows the fields extracted from the logs.

Examples:

```text
Username
Source_IP
Destination_IP
Country
Timestamp
Action
```

Clicking a field shows its common values.

For example:

```text
Country

India       45%
France      30%
USA         15%
Germany     10%
```

You can then use these values to create filters.

---

## `+` and `-` Filters

### `+`

Shows events containing that value.

Example:

```text
Country = India
```

### `-`

Excludes events containing that value.

Example:

```text
Country != India
```

### Simple meaning:

**Fields Pane = Choose what information you want to investigate**

---

# 3. Index Pattern

An **Index Pattern (definition that tells Kibana which Elasticsearch data to search)** tells Kibana where the required logs are stored.

Different types of logs may have different index patterns.

Example:

```text
VPN logs
   ↓
vpn_connections
```

For this lab, the required index pattern is:

```text
vpn_connections
```

---

## Why is Index Pattern Important?

Suppose the organization has:

```text
Windows Logs
VPN Logs
Firewall Logs
Web Logs
```

You don't want to search all of them when investigating a VPN login.

You select:

```text
vpn_connections
```

Then Kibana searches the relevant VPN data.

### Simple meaning:

**Index Pattern = Select which dataset/log source you want to investigate**

---

# 4. Search Bar

The **Search Bar (place where you enter queries)** is used to search and filter logs.

Example:

```text
Username: Maleena
```

You can use queries to find specific events.

It helps narrow down thousands of logs to only the events you need.

### Simple meaning:

**Search Bar = Ask Kibana to find specific logs**

---

# 5. Time Filter

The **Time Filter (filter that limits results to a specific time period)** allows you to choose when the events occurred.

Examples:

* Last 15 minutes
* Last 1 hour
* Last 24 hours
* Last 7 days
* Custom time range

---

## Why is it Important?

Suppose an attack happened on:

```text
January 11, 2022
```

If your time filter is set to:

```text
Last 15 minutes
```

you won't see those logs.

For this lab, make sure your time range includes:

**January 2022**

A wide option such as:

```text
Last 15 years
```

will also work.

### Simple meaning:

**Time Filter = Choose when to search**

---

# 6. Timeline

The **Timeline (graph showing the number of events over time)** appears above the logs.

Example:

```text
Events
  │
  │             █
  │             █
  │             █
  │      █      █
  │  █   █      █
  └──────────────────
     Jan 10 Jan 11
```

It shows how many events happened during different periods.

---

## Why is Timeline Useful?

It helps identify **spikes (sudden increase in activity)**.

Example:

```text
Normal activity:

████
████
████

Suddenly:

████████████████████
```

That spike could indicate:

* Brute-force attack
* Malware activity
* Scanning
* Data transfer
* Other unusual activity

You can click the spike to investigate the events during that period.

### Simple meaning:

**Timeline = See when activity increased or decreased**

---

# 7. Top Bar

The **Top Bar (navigation and action bar)** contains options to:

* Save searches.
* Open saved searches.
* Share searches.
* Save investigation results.
* Access other Kibana options.

### Simple meaning:

**Top Bar = Manage your searches and workspace**

---

# 8. Discover Tab

The **Discover Tab** is the main workspace where you:

* Search logs.
* Filter logs.
* Investigate events.
* Analyze suspicious activity.

### Simple meaning:

**Discover = Main investigation area**

---

# 9. Add Filter

Instead of manually typing a query, you can use **Add Filter**.

Example:

You want:

```text
Country = France
```

Click:

**Add Filter**

↓

Select:

```text
Country
```

↓

Select:

```text
France
```

↓

Kibana shows only matching logs.

### Simple meaning:

**Add Filter = Easily narrow down the logs**

---

# Create Table

By default, Kibana may display logs with lots of information.

This can create **noise (unnecessary information)**.

You can select important fields and create a table.

Example:

Instead of:

```text
100+ fields
```

Show only:

| Username | Source IP | Country | Action |
| -------- | --------- | ------- | ------ |
| Maleena  | 107.x.x.x | France  | Login  |
| John     | 192.x.x.x | USA     | Login  |

This makes investigation easier.

---

## Why Create a Table?

It helps:

* Reduce unnecessary information.
* Focus on important fields.
* Compare events easily.
* Present investigation results clearly.

You can also **save the table format** so the same fields appear later.

---

# Complete Investigation Flow

```text
Open Kibana
     ↓
Open Discover
     ↓
Select Index Pattern
     ↓
vpn_connections
     ↓
Set Time Filter
     ↓
Search Logs
     ↓
Use Fields / Add Filters
     ↓
Check Timeline
     ↓
Investigate Suspicious Events
     ↓
Create Useful Table
```

---

# Example SOC Investigation

Suppose you want to investigate suspicious VPN activity.

### Step 1

Select:

```text
Index Pattern:
vpn_connections
```

### Step 2

Set time range:

```text
January 2022
```

### Step 3

Look at the Timeline.

You notice:

```text
January 11
██████████████████
```

There is a sudden spike.

### Step 4

Click the spike.

Kibana shows the logs from that period.

### Step 5

Use the Fields Pane.

Check:

```text
Username
Source IP
Country
Action
```

### Step 6

Add filters to narrow the results.

### Step 7

Create a table containing only important fields.

Now you can investigate the suspicious activity much more easily.

--------------------------

# KQL (Kibana Query Language) 

## What is KQL?

**KQL (Kibana Query Language)** is a query language used in Kibana to **search and filter logs stored in Elasticsearch**.

You use KQL mainly in the **Search Bar** of the Discover tab.

There are two main types of searches:

1. **Free Text Search**
2. **Field-Based Search**

---

# 1. Free Text Search

Free text search means searching for a word or phrase **without specifying a field**.

Kibana searches for that text across the available data.

### Example

```text
security
```

This searches for logs containing the term `security`.

---

## Searching a Phrase

You can search for an exact phrase using quotes.

```text
"United States"
```

This searches for logs containing the phrase:

```text
United States
```

---

## Important: Whole Word Search

Suppose you search:

```text
United
```

It may not return results for:

```text
United States
```

because KQL can search based on the complete term/token rather than automatically treating `United` as a partial match.

---

# Wildcard `*`

The `*` wildcard means **match additional characters/text**.

### Example

```text
United*
```

This can match values beginning with:

```text
United
United States
United Kingdom
United Nations
```

### Simple meaning:

`*` = **anything after this**

---

# 2. Logical Operators

KQL supports:

```text
AND
OR
NOT
```

These are used to combine or exclude search conditions.

---

# AND Operator

**AND** means **both conditions must be present**.

### Example

```text
"United States" AND "Virginia"
```

Meaning:

> Find logs containing both **United States** and **Virginia**.

```text
United States
       +
Virginia
       ↓
Matching logs
```

### Remember:

**AND = Both**

---

# OR Operator

**OR** means **either condition can be present**.

### Example

```text
"United States" OR "England"
```

Meaning:

> Find logs containing either **United States** or **England**.

### Remember:

**OR = Either**

---

# NOT Operator

**NOT** is used to exclude something from the results.

### Example

```text
"United States" AND NOT ("Florida")
```

Meaning:

> Show United States logs but exclude Florida.

### Remember:

**NOT = Exclude**

---

# Logical Operators – Quick Table

| Operator | Meaning           | Example               |
| -------- | ----------------- | --------------------- |
| `AND`    | Both conditions   | `USA AND Virginia`    |
| `OR`     | Either condition  | `USA OR England`      |
| `NOT`    | Exclude something | `USA AND NOT Florida` |

---

# 3. Field-Based Search

Field-based search is more precise because you tell Kibana:

> **Which field** and **what value** you are looking for.

The basic syntax is:

```text
Field: Value
```

The `:` means **equals / contains this value**.

---

## Example

```text
Source_ip : 238.163.231.224
```

Meaning:

> Find logs where the `Source_ip` field contains `238.163.231.224`.

---

## Multiple Fields

You can combine multiple field searches using `AND`.

### Example

```text
Source_ip : 238.163.231.224 AND UserName : Suleman
```

Meaning:

> Find logs where:
>
> * Source IP = `238.163.231.224`
> * AND Username = `Suleman`

Both conditions must match.

---

# Free Text vs Field-Based Search

### Free Text

```text
Suleman
```

Searches for `Suleman` generally across the data.

### Field-Based

```text
UserName : Suleman
```

Searches specifically inside the `UserName` field.

Therefore:

> **Field-based search is usually better for SOC investigations because it is more precise.**

----------------------

# Kibana Visualization 

## What is Visualization?

**Visualization (showing data in an easy-to-understand visual form)** allows SOC analysts to represent Elasticsearch data using:

* Tables
* Pie charts
* Bar charts
* Other graphs

Instead of reading hundreds of logs, we can turn the data into a clear visual format.

---

# Create Visualization

You can create a visualization from the **Discover** tab.

### Basic Steps

1. Open **Discover**.
2. Select the required field.
3. Click **Visualization**.
4. Choose the type of visualization:

   * Table
   * Pie chart
   * Bar chart
   * etc.
5. Configure the required fields.
6. Save the visualization.

---

# Correlation

## What is Correlation?

**Correlation (connecting two or more fields to find a relationship)** helps analysts understand how different pieces of data are related.

### Example

Suppose we have:

```text
Source_IP
Source_Country
```

We can create a correlation between them.

Example:

| Source_IP     | Source_Country |
| ------------- | -------------- |
| 107.14.182.38 | France         |
| 107.3.206.58  | USA            |
| 192.168.1.20  | India          |

This helps us understand:

> **Which IP addresses are associated with which countries?**

---

# Creating a Table

We can also create a table using selected fields.

For this lab, we want:

```text
UserName
Source_ip
```

The result could look like:

| UserName | Source_ip     |
| -------- | ------------- |
| Maleena  | 107.14.182.38 |
| Suleman  | 107.3.206.58  |
| John     | 192.168.1.20  |

This is much easier to analyze than raw logs.

---

# Saving a Visualization

After creating a visualization:

### Step 1

Click **Save** at the top-right.

### Step 2

Enter:

* **Title** (name of the visualization)
* **Description** (what the visualization shows)

Example:

```text
Title:
Failed VPN Connections

Description:
Shows users and source IP addresses involved in failed VPN connection attempts.
```

### Step 3

Click:

**Save and add to library**

The visualization is now saved and can be reused later.

---

# Failed Connection Attempts Visualization

The lab asks us to create a table showing **failed VPN connection attempts**.

## Configuration

### Data View

Select:

```text
vpn_connections
```

### Time Range

Make sure the time picker includes:

```text
January 2022
```

### Filter

Filter:

```text
action: failed
```

This means:

> Show only VPN connections where the action was **failed**.

⚠️ **Important:** Do not exclude failed events. We specifically want the failed connection attempts.

---

# Table Fields

Use these two fields:

```text
UserName
Source_ip
```

The final table should show:

| UserName | Source_ip  |
| -------- | ---------- |
| User A   | IP Address |
| User B   | IP Address |
| User C   | IP Address |

This helps the SOC analyst quickly identify:

* **Who** failed to connect.
* **Which IP address** was used.

---

# Why Is This Useful for SOC?

Imagine you see:

```text
UserName: Maleena
Source_ip: 107.14.182.38
Action: failed
```

repeated hundreds of times.

This could indicate:

**VPN Brute Force (many login attempts trying to guess valid credentials).**

A visualization makes these patterns much easier to identify.

---

# Visualization Workflow

```text
Discover
   ↓
Select Data View
   ↓
Set Time Range
   ↓
Apply Filter
   ↓
Select Fields
   ↓
Create Visualization
   ↓
Choose Table / Chart
   ↓
Save
   ↓
Add to Library
```

------------------------

# Kibana Dashboards 
## What is a Dashboard?

A **Dashboard (single screen that combines multiple visualizations and searches)** provides a quick overview of security data.

Instead of checking each search or visualization separately, we can put everything together in one place.

For example, a VPN dashboard can show:

* Failed VPN connections
* Source IP addresses
* Usernames
* Countries
* Login activity
* Connection trends

---

# Why Do SOC Analysts Use Dashboards?

Dashboards provide **visibility (quick understanding of what is happening)**.

They help analysts:

* Monitor activity.
* Identify unusual patterns.
* Find spikes in events.
* Track failed logins.
* Investigate suspicious IPs.
* Quickly understand the overall situation.

---

# Creating a Custom Dashboard

We already have:

* **Saved Searches** from the Discover tab.
* **Saved Visualizations**.

Now we can combine them into one dashboard.

---

## Step 1 – Open Dashboard

Go to:

**Dashboard → Create Dashboard**

This creates a new empty dashboard.

---

## Step 2 – Add from Library

Click:

**Add from Library**

**Library (place where saved searches and visualizations are stored).**

You will see the searches and visualizations that you previously saved.

---

## Step 3 – Select Items

Select the required:

* Saved Searches
* Visualizations

They will be added to the dashboard.

Example:

```text
VPN Dashboard
────────────────────────────
Failed Connections | Login Activity

Source IPs         | Countries

VPN Activity Timeline
────────────────────────────
```

---

## Step 4 – Arrange the Dashboard

Move and resize the items so that the dashboard is easy to understand.

For example:

```text
┌─────────────────┬─────────────────┐
│ Failed Logins   │ Top Source IPs  │
├─────────────────┼─────────────────┤
│ Countries       │ User Activity   │
├─────────────────┴─────────────────┤
│       VPN Activity Timeline       │
└───────────────────────────────────┘
```

The exact layout depends on what information you want to monitor.

---

## Step 5 – Save

After arranging everything:

**Save the Dashboard**

Give it a meaningful name.

Example:

```text
VPN Security Dashboard
```

---

# Complete Workflow

```text
Discover
   ↓
Create Searches
   ↓
Save Searches
   ↓
Create Visualizations
   ↓
Save Visualizations
   ↓
Dashboard
   ↓
Create Dashboard
   ↓
Add from Library
   ↓
Add Searches + Visualizations
   ↓
Arrange
   ↓
Save Dashboard
```

---

# Simple Example

Suppose you are monitoring VPN activity.

You have already created:

**Search 1:**

```text
Failed VPN Connections
```

**Visualization 1:**

```text
Top Source IPs
```

**Visualization 2:**

```text
VPN Connections by Country
```

You can combine all three:

```text
┌──────────────────────────────────┐
│       VPN SECURITY DASHBOARD     │
├─────────────────┬────────────────┤
│ Failed          │ Top Source IPs │
│ Connections     │                │
├─────────────────┴────────────────┤
│ Connections by Country            │
└──────────────────────────────────┘
```

Now the SOC analyst can see important VPN activity **from one screen**.

----------------
