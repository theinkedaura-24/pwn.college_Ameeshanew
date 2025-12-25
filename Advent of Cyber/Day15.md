# Drone Alone

Investigate web attack forensics using Splunk to detect command injection attacks, analyze malicious payloads, and trace attacker activities from web logs to OS-level execution.

## Solution:

**Step 1: Accessing Splunk**

- Started AttackBox and target machine, waited 3 minutes for full boot
- Opened Firefox and navigated to `http://MACHINE_IP:8000`
- Logged in with provided credentials:
  - Username: `admin`
  - Password: `(provided in task)`

![Splunk login screen](https://via.placeholder.com/800x400?text=Splunk+Login)

- After successful login, reached Splunk Search & Reporting dashboard
- **Important**: Adjusted time range to "Last 7 days" or "All time" to ensure events were visible

![Splunk search dashboard](https://via.placeholder.com/800x400?text=Splunk+Dashboard)

**Step 2: Detecting Suspicious Web Commands**

- Goal: Identify HTTP requests containing signs of command execution attempts
- Executed Splunk query to search Apache access logs:

```
index=windows_apache_access (cmd.exe OR powershell OR "powershell.exe" OR "Invoke-Expression") | table _time host clientip uri_path uri_query status
```

**Query breakdown:**
- `index=windows_apache_access`: Search Apache web server logs
- Search terms: cmd.exe, powershell, Invoke-Expression (command execution indicators)
- `table`: Display specific fields in tabular format

![Query results showing suspicious requests](https://via.placeholder.com/800x400?text=Suspicious+Web+Commands)

**Decoding Base64 Payload:**

- Found encoded PowerShell string in results: `VABoAGkAcwAgAGkAcwAgAG4AbwB3ACAATQBpAG4AZQAhACAATQBVAEEASABBAEEASABBAEEA`
- Navigated to https://www.base64decode.org/
- Pasted the encoded string and clicked "Decode"
- Result: `This is now Mine! MUAHAHAA`

![Base64 decoding result](https://via.placeholder.com/800x400?text=Base64+Decoded)

This confirmed the attacker was injecting malicious commands into the web application.

**Step 3: Investigating Server-Side Errors**

- Goal: Check Apache error logs for execution attempts or internal failures
- Executed query:

```
index=windows_apache_error ("cmd.exe" OR "powershell" OR "Internal Server Error")
```

- Changed view to "View: Raw" using dropdown above Event display field

![Apache error logs showing exploitation attempts](https://via.placeholder.com/800x400?text=Apache+Error+Logs)

**Key findings:**
- Requests like `/cgi-bin/hello.bat?cmd=powershell` triggered "500 Internal Server Error"
- This indicates attacker's input was processed but failed during execution
- Confirms exploitation attempts reached the backend layer

**Step 4: Tracing Suspicious Process Creation**

- Goal: Identify malicious processes spawned by Apache web server
- Used Sysmon (System Monitor) logs to track process relationships:

```
index=windows_sysmon ParentImage="*httpd.exe"
```

- Changed view to "View: Table" for better visualization

![Sysmon process tree showing Apache spawning cmd.exe](https://via.placeholder.com/800x400?text=Process+Creation)

**Critical findings:**
- Apache (httpd.exe) spawned child processes it shouldn't normally create
- Discovered process chain:
  ```
  ParentImage: C:\Apache24\bin\httpd.exe
  Image: C:\Windows\System32\cmd.exe
  ```

**This is a strong indicator of successful command injection** - the web attack penetrated to the operating system level.

**Step 5: Confirming Attacker Enumeration**

- Goal: Verify post-exploitation reconnaissance activity
- Searched for common enumeration commands:

```spl
index=windows_sysmon *cmd.exe* *whoami*
```

![Whoami command execution logged](https://via.placeholder.com/800x400?text=Whoami+Enumeration)

**Analysis:**
- Found `whoami.exe` execution via cmd.exe
- Attackers commonly use `whoami` immediately after gaining code execution
- Purpose: Determine which user account the malicious process runs as
- This confirms the attacker achieved post-exploitation reconnaissance

**Step 6: Identifying Encoded PowerShell Payloads**

- Goal: Find all Base64-encoded PowerShell commands
- Final investigation query:

```
index=windows_sysmon Image="*powershell.exe" (CommandLine="*enc*" OR CommandLine="*-EncodedCommand*" OR CommandLine="*Base64*")
```

**What to look for:**
- PowerShell commands with `-EncodedCommand` or `Base64` parameters
- Common obfuscation technique to hide malicious intent
- If defenses worked correctly: No results (encoded payload blocked)
- If results appear: Decode Base64 to inspect attacker's true intent

![Encoded PowerShell command search](https://via.placeholder.com/800x400?text=Encoded+PowerShell)

**Attack Chain Reconstruction:**

Based on the investigation, the complete attack flow was:

1. **Initial Access**: Attacker sent malicious HTTP requests to `/cgi-bin/hello.bat` with command injection payloads
2. **Exploitation**: Vulnerable CGI script processed attacker input, executing system commands
3. **Command Execution**: Apache spawned `cmd.exe` and `powershell.exe` processes
4. **Reconnaissance**: Attacker ran `whoami` to identify privilege level
5. **Payload Delivery**: Attempted to execute Base64-encoded PowerShell commands

## Flags/Answers:

```
Reconnaissance executable: whoami.exe
Attacker attempted to run: powershell.exe
```

## Concepts learnt:

* **Command Injection**: Web vulnerability where attackers inject OS commands into application input fields, causing the server to execute arbitrary system commands. Occurs when applications pass unsanitized user input to system shells.

* **CGI (Common Gateway Interface)**: Legacy web technology that allows web servers to execute external programs. Historically vulnerable to command injection if input isn't properly validated. The `hello.bat` script was the vulnerable entry point.

* **Apache Access Logs**: Records of all HTTP requests received by Apache web server. Contains crucial forensic data: timestamps, client IPs, requested URIs, query parameters, and response status codes.

* **Apache Error Logs**: Captures server-side errors, failed requests, and application crashes. Internal Server Errors (500) often indicate exploitation attempts that partially succeeded but failed during execution.

* **Sysmon (System Monitor)**: Windows system service that logs detailed system activity including process creation, network connections, and file modifications. Provides process tree relationships (Parent-Child) critical for attack reconstruction.

* **Process Lineage/Tree**: Parent-child relationship between processes. Normal Apache operations only spawn worker threads, not system processes. Finding `httpd.exe` as parent of `cmd.exe` or `powershell.exe` is a major red flag.

* **Base64 Encoding**: Technique to encode binary data as ASCII text. Attackers use it to obfuscate malicious payloads, bypass simple string-based detection, and evade security filters.

* **PowerShell EncodedCommand**: PowerShell's `-EncodedCommand` parameter accepts Base64-encoded commands. Commonly abused by attackers to hide malicious scripts from defenders and evade basic detection mechanisms.

* **Post-Exploitation Reconnaissance**: Activities attackers perform after gaining initial access to understand the compromised environment. Common commands: `whoami`, `hostname`, `ipconfig`, `net user`.

* **Splunk SPL (Search Processing Language)**: Query language for searching and analyzing machine data in Splunk. Uses pipe operators (`|`) to chain commands and filter results.

* **Blue Team Investigation Workflow**: Systematic approach to incident response:
  1. Detect anomalies in logs
  2. Correlate evidence across data sources
  3. Trace attack progression
  4. Identify scope of compromise
  5. Document findings

## Notes:

* **Splunk investigation tips**:
  - Always check time range first - default may be too narrow
  - Use `| table` to display specific fields in organized format
  - Switch between "Raw" and "Table" views depending on data type
  - Combine multiple data sources (web logs + Sysmon) for complete picture

* **Key indicators of command injection**:
  - Unusual characters in URI parameters: `;`, `|`, `&`, `$`, backticks
  - System command keywords: cmd, powershell, bash, sh, whoami, net
  - 500 Internal Server Errors following suspicious requests
  - Unexpected process creation from web server processes

* **Why Base64 encoding is effective for attackers**:
  - Bypasses simple string-based WAF rules
  - Evades grep/regex searches in logs
  - Prevents command-line logging from capturing actual malicious content
  - Allows Unicode/special characters in commands

* **Normal vs abnormal Apache behavior**:
  - **Normal**: httpd.exe spawns only httpd.exe worker processes
  - **Abnormal**: httpd.exe spawns cmd.exe, powershell.exe, bash, or other system utilities
  - Any system command execution from web server = investigation required

* **Sysmon Event IDs relevant to this investigation**:
  - Event ID 1: Process Creation (used in all our queries)
  - Event ID 3: Network Connection
  - Event ID 11: File Creation
  - Sysmon provides much richer data than default Windows logs

* **Common attacker enumeration commands**:
  ```
  whoami          # Current user
  hostname        # Computer name  
  ipconfig        # Network info
  net user        # User accounts
  net localgroup  # Group memberships
  systeminfo      # OS details
  tasklist        # Running processes
  ```

* **Defense recommendations**:
  - Input validation: Sanitize all user input
  - Disable unnecessary CGI scripts
  - Run web servers with minimal privileges
  - Implement Web Application Firewall (WAF)
  - Enable comprehensive logging (Sysmon, web server logs)
  - Monitor for anomalous process creation
  - Alert on Base64-encoded commands

* **Alternative investigation tools**:
  - ELK Stack (Elasticsearch, Logstash, Kibana)
  - Graylog
  - Windows Event Viewer (less powerful than Splunk)
  - Custom Python scripts for log parsing

## Resources:

* [TryHackMe - Advent of Cyber 2025 Day 15](https://tryhackme.com/r/room/adventofcyber2025)
* [OWASP - Command Injection](https://owasp.org/www-community/attacks/Command_Injection)

* [Base64 Decode Tool](https://www.base64decode.org/)
* [MITRE ATT&CK - Command and Scripting Interpreter](https://attack.mitre.org/techniques/T1059/)
* [Blue Team Handbook - Incident Response](https://www.blueteamhandbook.com/)
