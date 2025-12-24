# old sAInt nick

Learn how AI can be used as an assistant in cyber security across various roles, domains, and tasks, while understanding important considerations surrounding AI deployment in security contexts.

## Solution:

**Step 1: Starting the Environment**

- Started both the AttackBox and target machine
- Waited approximately 2 minutes for both machines to fully boot
- Expanded AttackBox to full screen mode for better viewing experience

**Step 2: Understanding AI in Cyber Security**

Learned about AI's applications across different security domains:

**Defensive Security (Blue Team):**
- AI agents speed up detection, investigation, and response
- Process telemetry (logs, network flows, endpoint signals) continuously
- Add context to alerts automatically
- Automate responses like isolating infected devices, blocking suspicious emails, flagging unusual logins

**Offensive Security (Red Team):**
- Automate laborious reconnaissance and information gathering tasks
- Analyze scanner outputs and map attack surfaces
- Allow pentesters to focus on tasks requiring human expertise

**Software Development:**
- Act as virtual "colleague" for bouncing ideas
- Function as SAST/DAST scanners to audit code for vulnerabilities
- Identify security issues in written code

**Important Considerations:**
- AI output isn't always 100% correct and must be verified
- Risk of AI causing race conditions or overwhelming systems during pentesting
- Data privacy, model security, and transparency concerns
- Cannot assume AI-generated code is secure

**Step 3: Accessing Van SolveIT**

- Navigated to `http://MACHINE_IP` from the AttackBox
- Accessed Van SolveIT, TBFC's cyber security AI assistant
- Three showcase stages available: Red (exploit generation), Blue (log analysis), Software (code analysis)

**Step 4: Stage 1 - Red Team Exploit Generation**

- Requested Van SolveIT to generate a Python exploit script
- AI generated SQL injection exploit code targeting the vulnerable web application
- Encountered copying issue - couldn't directly copy code from chatbot

**Workaround:**
- Opened browser Inspect Element (F12)
- Located the code block in the HTML
- Selected all code with Ctrl+A
- Copied the entire code snippet

```
nano script.py
```

- Pasted the exploit code into nano editor
- Modified the `HOST` variable to match the target: `MACHINE_IP:5000`

**Step 5: Executing the Exploit**

- Saved the script and made it executable
- Ran the Python exploit script

```
python3 script.py
```

- The script successfully exploited the SQL injection vulnerability
- **Flag found in script output: THM{SQLI_EXPLOIT}**

![Exploit execution showing flag](https://github.com/user-attachments/assets/502384c0-155a-4e3a-a541-ebf3d08ff8b5)

**Step 6: Stage 2 - Blue Team Log Analysis**

- Van SolveIT analyzed web server logs for signs of attack
- AI identified suspicious patterns, attack vectors, and compromised endpoints
- Copied logs from terminal by right-clicking
- Pasted logs into Van SolveIT for analysis
- AI provided detailed breakdown of the attack timeline and indicators of compromise

**Step 7: Stage 3 - Software Vulnerability Analysis**

- Van SolveIT performed static code analysis
- Identified security vulnerabilities in provided source code
- AI explained the vulnerabilities and suggested remediation steps
- Highlighted common security issues like SQL injection points, XSS vulnerabilities, and insecure configurations

**Step 8: Completing All Stages**

- After progressing through all three stages (Red, Blue, Software)
- **Final flag presented: THM{AI_MANIA}**

## Flags:

```
THM{AI_MANIA}
THM{SQLI_EXPLOIT}
```

## Concepts learnt:

- **AI in Defensive Security**: AI agents automate threat detection, investigation, and response by processing vast amounts of telemetry data and providing contextual alerts in real-time
- **AI in Offensive Security**: AI automates reconnaissance, OSINT gathering, scanner output analysis, and attack surface mapping, allowing pentesters to focus on complex exploitation tasks
- **AI in Software Security**: AI serves as both a development assistant and security scanner (SAST/DAST) to identify vulnerabilities in code, though ironically struggles to write completely secure code itself
- **SAST (Static Application Security Testing)**: Analyzes source code without executing it to find security vulnerabilities
- **DAST (Dynamic Application Security Testing)**: Tests running applications to identify security issues during runtime
- **SQL Injection**: Database attack where malicious SQL queries are injected to manipulate or extract data from databases
- **Behavior Analysis**: AI tracks normal system behavior over time and flags anomalies that could indicate security incidents
- **Telemetry**: Automated collection and transmission of data from remote systems (logs, network flows, endpoint signals) for monitoring and analysis
- **Race Condition**: Programming flaw where system behavior depends on timing/sequence of events - can cause crashes or security issues
- **AI Hallucinations**: When AI generates false or nonsensical information presented as fact - critical consideration in security contexts
- **OSINT (Open Source Intelligence)**: Gathering information from publicly available sources for reconnaissance
- **Attack Surface**: All possible entry points where an attacker could potentially compromise a system

## Notes:

- Van SolveIT's responses could take 1-2 minutes to generate before appearing - patience required
- The chatbot could be restarted using the "Restart Chat" button if it became confused
- Stages unlocked progressively and could be revisited by clicking stage names in the top left
- Direct copying from the chatbot interface was problematic - using browser Inspect Element was a reliable workaround
- Alternative method for copying code: Could have used browser's "Copy outer HTML" or screenshot OCR
- The exploit script required manual IP address modification before execution
- Real-world consideration: Always verify AI-generated exploits in controlled environments before production use
- The room emphasized that AI is a tool to augment human capabilities, not replace them - especially critical in security
- AI's inability to write perfectly secure code while being good at finding vulnerabilities is an interesting paradox
- Important reminder: Never use AI-generated exploits against unauthorized targets - legal and ethical considerations apply
- The "vibe coding" reference highlights concerns about developers over-relying on AI without understanding the generated code
- Would have been interesting to see Van SolveIT's false positives or hallucinations as learning examples

## Resources:

- (https://tryhackme.com/room/defendingadverserialattacks)
- ](https://owasp.org/www-community/attacks/SQL_Injection)
- ](https://www.synopsys.com/glossary/what-is-sast.html)
- ](https://osintframework.com/)

***
