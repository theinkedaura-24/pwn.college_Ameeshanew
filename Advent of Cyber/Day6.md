# Egg-xecutable

> Learn the principles of malware analysis using sandboxes, understand the difference between static and dynamic analysis, and use professional tools like PeStudio, Regshot, and ProcMon to investigate suspicious executables.

## Solution:

**Step 1: Starting the Sandbox Environment**

- Started the target machine and waited approximately 2 minutes for full boot
- Accessed the malware analysis sandbox environment with pre-installed tools
- **Important**: Did NOT execute `HopHelper.exe` yet - waited for instructions
- Located the suspicious `HopHelper.exe` file in the "HopHelper" folder on Desktop

**Step 2: Understanding Malware Analysis Fundamentals**

**What is Malware Analysis?**
- Process of examining malicious files to understand functionality, operation, and defense methods
- Two main branches:
  - **Static Analysis**: Inspecting files WITHOUT executing them
  - **Dynamic Analysis**: Analyzing files BY executing them in controlled environment

**Why Use Sandboxes?**
- Safe, isolated environments for executing potentially malicious code
- Virtual machines with snapshotting capabilities
- **Golden Rule**: Never run dangerous applications on devices you care about
- Disposable digital play-pens that protect sensitive data and systems

**Step 3: Static Analysis with PeStudio**

**Information Gathered from Static Analysis:**
- **Checksums**: Unique identifiers (SHA256) for tracking and cataloging files
- **Strings**: Readable characters revealing IPs, URLs, commands, passwords
- **Imports**: Libraries and functions the application depends on
- **Resources**: Data like icons (malware often hides here or uses fake icons)

**Using PeStudio:**

- Launched PeStudio from Desktop shortcut
- Loaded `HopHelper.exe` by dragging and dropping into PeStudio window
- PeStudio automatically displayed file information

![PeStudio interface](https://github.com/user-attachments/assets/pestudio-example)

**Finding the SHA256 Checksum:**
- Located the `file > sha256` property in the information table
- **SHA256 Hash: `F29C270068F865EF4A747E2683BFA07667BF64E768B38FBB9A2750A3D879CA33`**
- This unique identifier can be used for threat intelligence and searching malware databases like VirusTotal

**Analyzing Strings:**
- Clicked on "strings" indicator in the left pane of PeStudio
- Sorted strings alphabetically to find relevant information
- Scrolled toward the bottom of strings output

![Strings analysis in PeStudio](https://github.com/user-attachments/assets/strings-analysis)

- **Flag found: `THM{STRINGS_FOUND}`**
- Additional findings: Could identify potential IPs, URLs, or command infrastructure

**Step 4: Dynamic Analysis - Preparing with Regshot**

**Understanding Regshot:**
- Creates "before" and "after" snapshots of Windows registry
- Compares snapshots to identify changes made by malware
- Common malware technique: Adding Run keys for persistence (auto-start on boot)

**Creating First Registry Snapshot:**

1. Launched Regshot from Desktop shortcut
2. Changed output directory to Desktop using the "..." button in "Output path" section
3. Clicked **1st shot** button
4. Selected **Shot** from dropdown
5. Waited a few minutes for snapshot completion

![Regshot first snapshot](https://github.com/user-attachments/assets/regshot-first-snapshot)

**Step 5: Executing the Malware**

- **Executed `HopHelper.exe`** from the HopHelper folder on Desktop
- Observed strange system behaviors (as warned by task)
- Malware now active in the sandbox environment

**Step 6: Capturing Registry Changes**

**Creating Second Registry Snapshot:**

1. Returned to Regshot
2. Clicked **2nd shot** button
3. Selected **Shot** from dropdown
4. Waited for second snapshot to complete
5. Clicked **Compare** button to analyze differences

![Regshot comparison](https://github.com/user-attachments/assets/regshot-compare)

**Analyzing Registry Modifications:**
- Text editor opened showing differences between snapshots
- Searched for the executable name within the log
- Identified persistence mechanism

**Registry Key Modified:**
```
HKU\S-1-5-21-1966530601-3185510712-10604624-1008\Software\Microsoft\Windows\CurrentVersion\Run\HopHelper
```

- This Run key ensures `HopHelper.exe` starts automatically on system boot
- Classic malware persistence technique

**Step 7: Dynamic Analysis with ProcMon**

**Understanding Process Monitor (ProcMon):**
- Part of Sysinternals suite
- Monitors and investigates how processes interact with Windows OS
- Shows real-time activities: registry operations, file operations, network connections
- Extremely detailed - requires filtering to find relevant information

**Setting Up ProcMon:**

1. Opened Process Monitor from Desktop shortcut
2. ProcMon automatically started capturing events from all processes
3. **Executed `HopHelper.exe` again** while ProcMon was capturing
4. Waited approximately 1 minute for full execution
5. Clicked **Play button** in toolbar to stop capturing events

![ProcMon capturing events](https://github.com/user-attachments/assets/procmon-capture)

**Step 8: Filtering ProcMon Output**

**Initial filtering - By Process Name:**

1. Clicked **Filter** button → **Filter** in dropdown
2. Created filter:
   - Field: **Process Name**
   - Condition: **is**
   - Value: **HopHelper.exe**
3. Clicked **Add** to apply filter
4. Clicked **OK** to save

- Output immediately became more manageable
- Now only showing `HopHelper.exe` activities

**Advanced Filtering - By Operation Type:**

To find network activity specifically:

1. Returned to Filter menu
2. Created additional filter:
   - Field: **Operation**
   - Condition: **contains**
   - Value: **TCP**
3. Clicked **Add** → **OK**

![ProcMon TCP filter](https://github.com/user-attachments/assets/procmon-tcp-filter)

**Analyzing Network Communication:**

**Operations of Interest:**
- `RegOpenKey` - Registry operations
- `CreateFile` - File system operations  
- `TCP Connect` - Network connections
- `TCP Receive` - Incoming network data

**Finding Network Protocol:**
- Filtered for TCP operations
- Examined the captured network activity
- **Protocol identified: `http`**
- Malware communicating using HTTP protocol (unencrypted)

**Additional Observations:**
- Could identify destination IPs and ports
- Potential C2 (Command & Control) server communication
- No HTTPS encryption - traffic could be monitored/blocked easily

## Flags:

```
SHA256 Hash: F29C270068F865EF4A747E2683BFA07667BF64E768B38FBB9A2750A3D879CA33
Static Analysis Flag: THM{STRINGS_FOUND}
Registry Key Modified: HKU\S-1-5-21-1966530601-3185510712-10604624-1008\Software\Microsoft\Windows\CurrentVersion\Run\HopHelper
Network Protocol: http
```

## Concepts learnt:

- **Malware Analysis**: Process of examining malicious files to understand functionality, operation, and develop defense strategies
- **Sandbox Environment**: Isolated, disposable virtual machine for safely executing and analyzing malware without risking production systems
- **Static Analysis**: Examining malware without execution - inspecting checksums, strings, imports, resources to understand potential behavior
- **Dynamic Analysis**: Executing malware in controlled environment to observe actual behavior and system interactions
- **SHA256 Checksum**: Cryptographic hash function producing unique 256-bit identifier - used for file identification and threat intelligence
- **Strings**: Readable character sequences within executables revealing IPs, URLs, commands, or credentials
- **Imports**: Libraries and functions an application uses - reveals how malware interacts with operating system
- **Resources**: Embedded data in executables (icons, images) - malware often uses fake icons or hides payloads here
- **Obfuscation**: Techniques attackers use to obscure code appearance/functionality - evades antivirus and analyst detection
- **Persistence Mechanism**: Methods malware uses to survive system reboots - commonly via Windows Run registry keys
- **Windows Registry**: Hierarchical database storing system and application configuration - prime target for malware persistence
- **Run Keys**: Registry keys specifying programs to auto-start on boot - `HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run`
- **Regshot**: Registry comparison tool creating before/after snapshots to identify malware-induced registry changes
- **ProcMon (Process Monitor)**: Sysinternals tool monitoring real-time system activity - file operations, registry operations, network connections
- **Sysinternals Suite**: Collection of advanced Windows system utilities for troubleshooting, diagnostics, and security analysis
- **Threat Intelligence**: Information about malware characteristics (hashes, IPs, behaviors) shared across security community
- **VirusTotal**: Online service analyzing suspicious files/URLs using multiple antivirus engines and sandboxes
- **C2 (Command and Control)**: Infrastructure attackers use to communicate with and control compromised systems
- **HTTP Protocol**: Unencrypted web protocol - malware using HTTP is easier to detect and block than HTTPS

## Notes:

- **Never execute malware outside sandbox environments** - this is the golden rule of malware analysis
- Virtual machines are ideal for sandboxing because of snapshot/restore capabilities - can easily revert to clean state
- Static analysis is always performed BEFORE dynamic analysis - safer and provides initial intelligence
- Checksums (especially SHA256) are crucial for threat intelligence - can search them on VirusTotal, Hybrid-Analysis, etc.
- Obfuscation techniques mean static analysis findings must be verified through dynamic analysis
- Alternative to PeStudio: Could use `strings` command on Linux, PE-bear, or CFF Explorer
- Registry Run keys location varies: `HKCU\...\Run` (current user) vs `HKLM\...\Run` (all users)
- ProcMon generates massive amounts of data - filtering is essential for effective analysis
- Can remove individual filters in ProcMon or use "Reset Filter" to start over
- Bonus challenge mentioned finding the web panel - would require examining full URLs in ProcMon TCP operations
- HTTP usage by malware is interesting - modern malware typically uses HTTPS to evade detection
- Could have used network monitoring tools like Wireshark alongside ProcMon for deeper packet analysis
- The HKU (HKEY_USERS) registry hive stores user-specific settings - the long SID identifies specific user account
- Real-world malware analysis often requires multiple iterations of static and dynamic analysis
- Advanced techniques include debugging, code deobfuscation, and reverse engineering with IDA Pro or Ghidra
- Some malware employs anti-analysis techniques: VM detection, debugger detection, time-based delays
- Always document findings thoroughly - timestamps, hashes, behaviors, IOCs (Indicators of Compromise)

## Resources:

- [TryHackMe Basic Static Analysis Room](https://tryhackme.com/room/staticanalysis1)
- [TryHackMe Basic Dynamic Analysis Room](https://tryhackme.com/room/dynamicanalysis)
  
- [Practical Malware Analysis Book](https://nostarch.com/malware)

***
