# Registry Furensics

Learn about Windows Registry structure, forensic analysis techniques, and investigate a compromised system using Registry Explorer to uncover malicious persistence mechanisms.

## Solution:

**Step 1: Understanding Windows Registry**

The Windows Registry acts as the operating system's "brain" - a hierarchical database storing all configuration settings, user preferences, installed applications, and system behavior.

**Registry structure:**
- **Registry Hives**: Physical files on disk containing registry data
- **Root Keys**: Logical organization visible in Registry Editor
- Registry data stored in binary format requiring special tools to read

**Key Registry Hives and their locations:**

| Hive | Contains | Location |
|------|----------|----------|
| SYSTEM | Services, drivers, boot config, hardware | C:\Windows\System32\config\SYSTEM |
| SECURITY | Security policies, audit settings | C:\Windows\System32\config\SECURITY |
| SOFTWARE | Installed programs, OS version, autostarts | C:\Windows\System32\config\SOFTWARE |
| SAM | User accounts, password hashes, groups | C:\Windows\System32\config\SAM |
| NTUSER.DAT | Recent files, user preferences | C:\Users\username\NTUSER.DAT |
| USRCLASS.DAT | Shellbags, jump lists | C:\Users\username\AppData\Local\Microsoft\Windows\USRCLASS.DAT |

![Windows Registry structure diagram](https://via.placeholder.com/800x400?text=Registry+Structure)

**Step 2: Mapping Hives to Root Keys**

Registry Editor displays hives through organized Root Keys:

| Hive File | Registry Editor Location |
|-----------|-------------------------|
| SYSTEM | HKEY_LOCAL_MACHINE\SYSTEM |
| SECURITY | HKEY_LOCAL_MACHINE\SECURITY |
| SOFTWARE | HKEY_LOCAL_MACHINE\SOFTWARE |
| SAM | HKEY_LOCAL_MACHINE\SAM |
| NTUSER.DAT | HKEY_USERS\<SID> and HKEY_CURRENT_USER |
| USRCLASS.DAT | HKEY_USERS\<SID>\Software\Classes |

**Step 3: Launching Registry Explorer**

- Connected to target machine `dispatch-srv01` via RDP or split view
- Clicked Registry Explorer icon on taskbar
- Tool opened with empty interface ready to load offline hives

![Registry Explorer on desktop](https://via.placeholder.com/800x400?text=Registry+Explorer+Launch)

**Why Registry Explorer over Registry Editor?**
- Can load offline hives (Registry Editor requires live system)
- Parses binary data into human-readable format
- Prevents accidental modifications to evidence
- Supports transaction log replay for "dirty" hives

**Step 4: Loading Registry Hives**

- Navigated to: `C:\Users\Administrator\Desktop\Registry Hives`
- Clicked **File → Load hive**
- Selected **SYSTEM** hive first

**Critical technique - Loading with transaction logs:**
1. Selected SYSTEM hive file
2. Held **SHIFT** while clicking **Open**
3. This automatically loads associated transaction log files (.LOG, .LOG1, .LOG2)
4. Received confirmation: "Transaction logs replayed"
5. Ensures clean, consistent hive state for forensic analysis

![Loading hive with transaction logs](https://via.placeholder.com/800x400?text=Load+Hive+Process)

**Step 5: Verifying System Information**

**Practice navigation - Finding computer name:**

- Navigated to: `ROOT\ControlSet001\Control\ComputerName\ComputerName`
- Alternatively: Searched "ComputerName" in search bar
- Or used **Available Bookmarks** tab
- Found hostname in Data value: **DISPATCH-SRV01**

![Computer name in Registry Explorer](https://via.placeholder.com/800x400?text=ComputerName+Registry+Key)

This confirmed we were analyzing the correct compromised system.

**Step 6: Investigating Installed Applications**

**Goal**: Find application installed before abnormal activity (October 21, 2025)

**Forensic key to check**: According to the registry forensics table:
```
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
```

**Investigation steps:**

1. Loaded **SOFTWARE** hive (with SHIFT+Open for transaction logs)
2. Navigated to: `ROOT\Microsoft\Windows\CurrentVersion\Uninstall`
3. Examined all subkeys for installation dates and program names
4. Filtered by timestamp before October 21, 2025

![Uninstall registry key showing installed programs](https://via.placeholder.com/800x400?text=Installed+Programs)

**Found suspicious application:**
- **Name**: DroneManager Updater
- **Installation date**: Before October 21, 2025 (before abnormal activity started)
- This immediately flagged as potential malicious software

**Answer 1**: `DroneManager Updater`

**Step 7: Tracing Application Execution Path**

**Forensic key to check**: Programs run by user stored in:
```
HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\RunMRU
```

However, for full path of installed applications, also checked:
```
HKLM\Software\Microsoft\Windows\CurrentVersion\App Paths
```

**Investigation:**

1. Loaded **NTUSER.DAT** hive for user-specific data
2. Searched for "DroneManager" across multiple registry paths
3. Located installation path in SOFTWARE hive under Uninstall key
4. Found DisplayIcon or InstallLocation value pointing to executable

![Execution path in registry](https://via.placeholder.com/800x400?text=Application+Execution+Path)

**Discovered full path:**
```
C:\Users\dispatch.admin\Downloads\DroneManager_Setup.exe
```

This reveals:
- User `dispatch.admin` downloaded and executed the malware
- File came from Downloads folder (common phishing/download vector)
- Executable named "Setup" to appear legitimate

**Answer 2**: `C:\Users\dispatch.admin\Downloads\DroneManager_Setup.exe`

**Step 8: Identifying Persistence Mechanism**

**Goal**: Find how malware maintains persistence after reboot

**Forensic key to check**: Startup programs stored at:
```
HKLM\Software\Microsoft\Windows\CurrentVersion\Run
HKCU\Software\Microsoft\Windows\CurrentVersion\Run
```

**Investigation:**

1. Still in SOFTWARE hive, navigated to: `ROOT\Microsoft\Windows\CurrentVersion\Run`
2. Examined all values for suspicious entries
3. Looked for DroneManager-related executables

![Startup persistence registry key](https://via.placeholder.com/800x400?text=Startup+Persistence)

**Found malicious startup entry:**
```
"C:\Program Files\DroneManager\dronehelper.exe" --background
```

**Analysis of persistence mechanism:**
- `dronehelper.exe`: Helper binary installed by DroneManager
- `--background` flag: Runs silently without user interaction
- Location: Program Files (requires admin privileges to install)
- Effect: Launches automatically every time system boots

**Answer 3**: `"C:\Program Files\DroneManager\dronehelper.exe" --background`

**Step 9: Complete Attack Timeline**

Reconstructed attack flow from registry evidence:

1. **Initial Compromise**: User `dispatch.admin` downloaded `DroneManager_Setup.exe` from Downloads
2. **Execution**: User ran the malicious installer
3. **Installation**: DroneManager installed to Program Files
4. **Persistence**: Created startup entry for `dronehelper.exe --background`
5. **Result**: Malware runs automatically on every boot, affecting SOCMAS drone operations

![Complete attack timeline](https://via.placeholder.com/800x400?text=Attack+Timeline)

## Flags/Answers:

```
Application installed before abnormal activity: DroneManager Updater
Full path of application execution: C:\Users\dispatch.admin\Downloads\DroneManager_Setup.exe
Persistence value added: "C:\Program Files\DroneManager\dronehelper.exe" --background
```

## Concepts learnt:

* **Windows Registry**: Hierarchical database storing Windows configuration data, user settings, installed applications, and system behavior. Acts as the OS "brain" containing everything needed for system functioning.

* **Registry Hives**: Physical binary files on disk that store registry data. Cannot be opened directly - require special tools. Examples: SYSTEM, SOFTWARE, SAM, SECURITY, NTUSER.DAT, USRCLASS.DAT.

* **Registry Root Keys**: Logical organizational structure visible in Registry Editor (HKEY_LOCAL_MACHINE, HKEY_CURRENT_USER, etc.). Hives are mapped to these root keys for human navigation.

* **Registry Forensics**: Process of extracting and analyzing evidence from Windows Registry to investigate security incidents, malware infections, user activity, and system changes. Critical component of digital forensics.

* **Offline Registry Analysis**: Examining registry hives outside the live system to prevent evidence modification and enable detailed investigation without system contamination.

* **Registry Explorer**: Open-source forensic tool for analyzing offline registry hives. Parses binary data into readable format, supports transaction log replay, and provides bookmarked access to common forensic keys.

* **Transaction Logs**: Additional files (.LOG, .LOG1, .LOG2) that record registry changes. Loading hives with transaction logs (SHIFT+Open) ensures complete, consistent data even from "dirty" hives with incomplete transactions.

* **Persistence Mechanisms**: Techniques malware uses to maintain presence after reboot. Common registry locations: Run keys, Services, Scheduled Tasks, AppInit_DLLs, Winlogon, Shell extensions.

* **Run Keys**: Registry locations (`HKLM/HKCU\...\Run`) that automatically execute programs at startup. Heavily abused by malware for persistence.

* **Forensic Artifacts**: Evidence left behind by user/system activity. Registry contains rich artifacts: recent files, typed paths, search terms, USB devices, installed programs, user assist, shellbags.

## Notes:

* **Key registry paths for forensic investigations**:

| Registry Path | Evidence Type |
|--------------|---------------|
| `HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist` | Recently accessed GUI applications |
| `HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\TypedPaths` | Paths typed in Explorer address bar |
| `HKLM\Software\Microsoft\Windows\CurrentVersion\App Paths` | Application installation paths |
| `HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\WordWheelQuery` | Explorer search terms |
| `HKLM\Software\Microsoft\Windows\CurrentVersion\Run` | System-wide startup programs |
| `HKCU\Software\Microsoft\Windows\CurrentVersion\Run` | User-specific startup programs |
| `HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs` | Recently accessed documents |
| `HKLM\SYSTEM\CurrentControlSet\Control\ComputerName\ComputerName` | System hostname |
| `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall` | Installed programs and metadata |
| `HKLM\SYSTEM\CurrentControlSet\Enum\USBSTOR` | Connected USB devices history |

* **Why offline analysis is critical**:
  - Live system analysis risks modifying evidence (timestamps, access records)
  - Running malware could detect forensic tools and hide evidence
  - Offline analysis provides forensically sound methodology
  - Transaction log replay ensures data integrity

* **Registry hive collection best practices**:
  - Copy hives from live system or forensic image
  - Include transaction logs (.LOG files) alongside hives
  - Collect both SYSTEM and user hives (NTUSER.DAT, USRCLASS.DAT)
  - Document collection time and hash values
  - Maintain chain of custody

* **Common persistence locations attackers use**:
  ```
  HKLM\Software\Microsoft\Windows\CurrentVersion\Run
  HKCU\Software\Microsoft\Windows\CurrentVersion\Run
  HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce
  HKLM\System\CurrentControlSet\Services
  HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon
  HKLM\System\CurrentControlSet\Control\Session Manager
  ```

* **Malware naming conventions to appear legitimate**:
  - "Updater", "Helper", "Manager", "Service"
  - Generic names like "svchost", "explorer" (slight variations)
  - Using `--background` or `-silent` flags to hide execution

* **Timeline analysis tips**:
  - Note: Abnormal activity started October 21, 2025
  - Look for installations/changes before this date
  - Compare with known-good baseline if available
  - Check LastWrite timestamps on registry keys

* **Other forensic tools for registry analysis**:
  - **RegRipper**: Automated registry parsing with plugins
  - **Registry Viewer** (AccessData): Commercial tool
  - **RECmd** (Eric Zimmerman): Command-line registry tool
  - **Autopsy**: Full forensic suite with registry analysis

* **User SID (Security Identifier)**:
  - Each user account has unique SID
  - NTUSER.DAT location: `HKEY_USERS\<SID>`
  - Can correlate SID to username via SAM hive

* **Real-world investigation workflow**:
  1. Collect registry hives from compromised system
  2. Load hives offline in forensic workstation
  3. Check persistence mechanisms (Run keys, Services)
  4. Review recently installed software
  5. Examine user activity (Recent Docs, Typed Paths, User Assist)
  6. Correlate with other artifacts (event logs, file system, memory)
  7. Build timeline of attacker activity
  8. Document findings for incident response

## Resources:

* [TryHackMe - Advent of Cyber 2025 Day 16](https://tryhackme.com/r/room/adventofcyber2025)
* [TryHackMe - Expediting Registry Analysis Room](https://tryhackme.com/room/expeditingregistryanalysis)
