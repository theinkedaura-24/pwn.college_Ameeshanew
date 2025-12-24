# Did you SIEM?

 Learn how to ingest and interpret custom log data in Splunk, create custom field extractions, and use Search Processing Language (SPL) to conduct a security investigation into a ransomware attack.

## Solution:

**Step 1: Accessing the Splunk Instance**

- Started the target machine and waited 2-3 minutes for it to fully boot
- Accessed the Splunk SIEM by visiting the provided reverse proxy URL in the browser
- Note: If encountering a 502 error, waited additional time for Splunk to fully initialize
- Clicked on **Search & Reporting** in the left panel to begin the investigation

**Step 2: Exploring the Pre-Ingested Data**

- Started with a basic search query to view all ingested logs

```
index=main
```

- Set the time frame to **All time** from the dropdown menu
- Identified two separate datasets by clicking on the `sourcetype` field:
  - **web_traffic**: Events related to web connections to/from the web server
  - **firewall_logs**: Firewall logs showing allowed/blocked traffic
  - Web server local IP: `10.10.1.15`

![Splunk interface showing datasets](https://github.com/user-attachments/assets/4b3c88fa-20fc-4f66-a770-0157fcf3b443)

**Step 3: Initial Triage of Web Traffic**

- Filtered to examine only web traffic logs

```
index=main sourcetype=web_traffic
```

- Analyzed the Splunk interface components:
  - **Timeline**: Showed distribution of 17,172 events with a distinctive traffic spike (attack window)
  - **Selected fields**: Basic metadata (host, source, sourcetype)
  - **Interesting fields**: Automatically extracted fields like `user_agent`, `path`, `client_ip`, `status`

**Step 4: Visualizing the Attack Timeline**

- Created a timechart to identify the day with abnormal log volume

```
index=main sourcetype=web_traffic | timechart span=1d count
```

- Switched to the **Visualization** tab to see the graph representation
- Sorted results in descending order to identify the peak day

```
index=main sourcetype=web_traffic | timechart span=1d count | sort by count | reverse
```

- **Peak traffic day identified: 2025-10-12**

**Step 5: Anomaly Detection - Examining Suspicious Fields**

Analyzed three key fields for suspicious activity:

**User Agent Field:**
- Found suspicious user agents beyond legitimate Mozilla/Chrome/Safari/Firefox
- Noticed automated tool signatures (curl, wget, sqlmap)

**Client IP Field:**
- Identified one particular IP address standing out with high activity volume

**Path Field:**
- Discovered suspicious URIs indicating potential attacks (path traversal, SQL injection attempts)

**Step 6: Filtering Out Legitimate Traffic**

- Excluded common legitimate user agents to focus on suspicious activity

```
index=main sourcetype=web_traffic user_agent!=*Mozilla* user_agent!=*Chrome* user_agent!=*Safari* user_agent!=*Firefox*
```

- Clicked on `client_ip` field and identified a single IP responsible for all suspicious traffic
- **Attacker IP identified: 198.51.100.55**

**Step 7: Narrowing Down to Top Suspicious IPs**

- Used statistics to confirm the top attacking IP

```
sourcetype=web_traffic user_agent!=*Mozilla* user_agent!=*Chrome* user_agent!=*Safari* user_agent!=*Firefox* | stats count by client_ip | sort -count | head 5
```

**Step 8: Tracing the Attack Chain**

**Phase 1 - Reconnaissance (Footprinting):**
- Searched for initial probing of configuration files

```
sourcetype=web_traffic client_ip="198.51.100.55" AND path IN ("/.env", "/*phpinfo*", "/.git*") | table _time, path, user_agent, status
```

- Confirmed attacker used low-level tools (curl, wget)
- Status codes: 404/403/401

**Phase 2 - Enumeration (Vulnerability Testing):**
- Searched for path traversal and open redirect attempts

```
sourcetype=web_traffic client_ip="198.51.100.55" AND path="*..\/..\/*" OR path="*redirect*" | stats count by path
```

- **Path traversal attempts: 658**
- Revealed attempts to read system files using `../../` sequences

**Phase 3 - SQL Injection Attack:**
- Searched for SQL injection tool signatures

```
sourcetype=web_traffic client_ip="198.51.100.55" AND user_agent IN ("*sqlmap*", "*Havij*") | table _time, path, status
```

- **Havij user_agent events: 993**
- Confirmed time-based SQL injection with `SLEEP(5)` payloads
- Status code 504 indicated successful time-based attacks

**Phase 4 - Data Exfiltration Attempts:**
- Searched for attempts to download sensitive files

```
sourcetype=web_traffic client_ip="198.51.100.55" AND path IN ("*backup.zip*", "*logs.tar.gz*") | table _time path, user_agent
```

- Identified exfiltration of compressed log files using curl, zgrab

**Phase 5 - Ransomware Staging & Remote Code Execution:**
- Searched for webshell and ransomware indicators

```
sourcetype=web_traffic client_ip="198.51.100.55" AND path IN ("*bunnylock.bin*", "*shell.php?cmd=*") | table _time, path, user_agent, status
```

- Confirmed successful webshell deployment
- Identified ransomware execution: `/shell.php?cmd=./bunnylock.bin`
- This represented Remote Code Execution (RCE) - full server compromise

**Step 9: Correlating with Firewall Logs**

**C2 Communication Analysis:**
- Pivoted to firewall logs to trace Command & Control communication

```
sourcetype=firewall_logs src_ip="10.10.1.5" AND dest_ip="198.51.100.55" AND action="ALLOWED" | table _time, action, protocol, src_ip, dest_ip, dest_port, reason
```

- Confirmed outbound C2 connection from compromised server to attacker IP
- Reason field showed: `C2_CONTACT`

**Data Exfiltration Volume:**
- Calculated total bytes transferred to C2 server

```
sourcetype=firewall_logs src_ip="10.10.1.5" AND dest_ip="198.51.100.55" AND action="ALLOWED" | stats sum(bytes_transferred) by src_ip
```

- **Bytes transferred to C2 server: 126,167 bytes**

## Flags:

```
Attacker IP: 198.51.100.55
Peak traffic day: 2025-10-12
Havij user_agent count: 993
Path traversal attempts: 658
Bytes transferred to C2: 126167
```

## Concepts learnt:

- **SIEM (Security Information and Event Management)**: Splunk is a SIEM platform that aggregates, analyzes, and correlates security logs from multiple sources to detect threats
- **SPL (Search Processing Language)**: Splunk's query language for searching, filtering, and manipulating data using pipes, commands, and functions
- **Log Ingestion**: The process of importing raw log data into Splunk for analysis and correlation
- **Field Extraction**: Splunk automatically or manually extracts structured fields (like `client_ip`, `user_agent`, `path`) from raw log data
- **Timechart**: SPL command to visualize event counts over time periods, useful for identifying anomalous activity spikes
- **Attack Chain Tracing**: Following an attacker's progression through multiple phases: reconnaissance → enumeration → exploitation → post-exploitation
- **Reconnaissance**: Initial probing phase where attackers scan for exposed files, configuration details, and vulnerabilities
- **Path Traversal**: Attack technique using `../../` sequences to access files outside the web root directory
- **SQL Injection**: Database attack using malicious SQL queries; time-based attacks use `SLEEP()` functions to confirm vulnerabilities
- **Remote Code Execution (RCE)**: Critical vulnerability allowing attackers to execute arbitrary commands on the target system
- **Webshell**: Malicious script (like `shell.php`) uploaded to servers to provide remote command execution capabilities
- **Command & Control (C2)**: Communication channel between compromised systems and attacker infrastructure
- **Data Exfiltration**: Unauthorized transfer of sensitive data from compromised systems to attacker-controlled servers
- **Log Correlation**: Linking events across multiple log sources (web logs + firewall logs) to build complete attack timeline

## Notes:

- The `-` symbol in `sort -count` reverses the sort order (equivalent to using `| reverse`)
- Alternative to filtering legitimate user agents: Could have used a whitelist approach with `user_agent IN (...)` for known malicious agents
- The `| stats count by` command is extremely useful for aggregating and summarizing data
- Status code 504 (Gateway Timeout) often indicates successful time-based SQL injection when combined with `SLEEP()` commands
- Real-world investigations would typically tighten the time range after identifying the attack window
- The `| table` command formats output into tabular view with specified fields only
- The `| head 5` command limits results to top 5 entries (useful for large datasets)
- Could have also used `| tail` to see the last events or `| dedup` to remove duplicate entries
- The attack followed the classic cyber kill chain: Reconnaissance → Weaponization → Delivery → Exploitation → Installation → C2 → Actions on Objectives
- Double-extortion ransomware typically involves both encrypting data AND exfiltrating it for leverage
- The `..\` characters needed escaping in SPL queries as `..\/`

***
