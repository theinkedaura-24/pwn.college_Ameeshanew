# Phishmas Greetings

Learn to identify phishing emails, understand the difference between spam and phishing, and recognize trending phishing techniques used by attackers to steal credentials and compromise systems.

## Solution:

**Step 1: Accessing the Email Threat Inspector**

- Started the target machine and waited 2-3 minutes for full deployment
- Accessed Wareville's Email Threat Inspector at `https://LAB_WEB_URL.p.thmlabs.com`
- Interface displayed multiple suspicious emails requiring triage

![Wareville Email Threat Inspector Interface](https://via.placeholder.com/800x400?text=Email+Threat+Inspector)

**Step 2: Understanding Phishing vs Spam**

**Phishing characteristics:**
- Precision attacks targeting specific users
- Intent to steal credentials, deliver malware, or commit fraud
- Uses social engineering and impersonation
- Poses real security threats

**Spam characteristics:**
- Bulk messages sent to mass audiences
- Marketing, promotions, or clickbait
- Annoying but mostly harmless
- No malicious intent (usually)

**Step 3: Learning Phishing Detection Techniques**

**Key indicators to identify:**

1. **Impersonation**: Sender pretending to be trusted person/organization
2. **Social Engineering**: Manipulating emotions (urgency, fear, curiosity)
3. **Typosquatting**: Domains with common misspellings (glthub.com vs github.com)
4. **Punycode**: Unicode characters replacing ASCII (тryhackme.com using Cyrillic)
5. **Spoofing**: Faked sender address, check SPF/DKIM/DMARC in headers
6. **Malicious Attachments**: Files designed to steal data or install malware
7. **Fake Login Pages**: Mimicking legitimate sites to steal credentials
8. **Side Channel Communication**: Moving conversation off-email (WhatsApp, SMS)

**Step 4: Email Analysis - Detailed Walkthrough**

**Email 1: PayPal Invoice Scam**

- Analyzed sender domain and email headers
- Identified fake invoice creating urgency
- Key indicators detected:
  - **Spoofing**: Failed SPF/DKIM/DMARC authentication
  - **Social Engineering**: Fake invoice with urgent payment request
  - **Sense of Urgency**: Pressure tactics to act quickly

![Email 1 analysis showing spoofing indicators](https://via.placeholder.com/800x400?text=Email+1+PayPal+Scam)

Selected categories: `Spoofing`, `Social Engineering Text`, `Sense of Urgency`

**Flag:** `THM{yougotnumber1-keep-it-going}`

---

**Email 2: Fake Voice Message from McSkidy**

- Examined the "From:" field showing `mcskidy@thebfc.com`
- Checked email headers - found failed authentication results
- Spotted malicious .html attachment disguised as voice message

```
Authentication-Results: SPF=fail, DKIM=fail, DMARC=fail
Return-Path: zxwsedr@easterbb.com
```

Key indicators:
- **Impersonation**: Pretending to be McSkidy
- **Spoofing**: Return-Path revealed real sender domain (easterbb.com)
- **Malicious Attachment**: .html file that can execute without browser sandboxing

![Email 2 with spoofed sender and malicious attachment](https://via.placeholder.com/800x400?text=Email+2+Voice+Message)

Selected categories: `Impersonation`, `Spoofing`, `Malicious Attachment`

**Flag:** `THM{nmumber2-was-not-tha-thard!}`

---

**Email 3: Urgent VPN Access Request**

- Subject: "URGENT: McSkidy VPN access for incident response"
- Sender using free Gmail domain instead of corporate domain
- Body contained multiple social engineering tactics:

```
From: mcskidy@gmail.com (RED FLAG: Free domain)
Body: "URGENT... immediately... don't contact via phone/email"
Request: VPN credentials needed NOW
```

Key indicators:
- **Impersonation**: Acting as McSkidy using external domain
- **Social Engineering Text**: Multiple manipulation techniques
- **Sense of Urgency**: Words like "urgent", "immediately"
- **Side Channel Request**: Discouraging standard communication methods

![Email 3 urgent VPN access request](https://via.placeholder.com/800x400?text=Email+3+VPN+Request)

Selected categories: `Impersonation`, `Social Engineering Text`, `Sense of Urgency`

**Flag:** `THM{Impersonation-is-aral-thing-keepIt}`

---

**Email 4: Laptop Upgrade Agreement via Dropbox**

- Subject: "TBFC-IT shared 'Christmas Laptop Upgrade Agreement' with you"
- Examined sender domain closely - discovered punycode usage

```
From: it-support@theƒbc.com
(Note: Latin letter ƒ instead of normal f!)

Headers showed:
Return-Path: xn--thebbc-[encoded]@attacker.com
```

- Legitimate Dropbox/OneDrive link used to bypass filters
- Attractive proposal to lure victims

Key indicators:
- **Impersonation**: Pretending to be IT department
- **External Sender Domain**: Not from legitimate TBFC domain
- **Typosquatting/Punycode**: Unicode character ƒ in domain
- **Social Engineering**: Appealing laptop upgrade offer

![Email 4 with punycode domain and Dropbox link](https://via.placeholder.com/800x400?text=Email+4+Laptop+Upgrade)

Selected categories: `Impersonation`, `External Sender Domain`, `Social Engineering Text`

**Flag:** `THM{Get-back-SOC-mas!!}`

---

**Email 5: Marketing Spam**

- Subject: Logistics solutions for SOC-mas event
- Analysis showed:
  - Legitimate sender domain
  - Pure marketing intent
  - No malicious indicators
  - No social engineering tactics
  - Just unsolicited advertising

**Classification:** This is **SPAM**, not phishing!

![Email 5 legitimate marketing spam](https://via.placeholder.com/800x400?text=Email+5+Spam)

Selected: `Spam`

**Flag:** `THM{It-was-just-a-sp4m!!}`

---

**Email 6: Document Sharing Scam**

- Similar pattern to Email 4
- Used legitimate file-sharing service
- Employed punycode or typosquatting in domain
- Social engineering text to create appeal

Key indicators:
- **Impersonation**: Fake corporate sender
- **Typosquatting/Punycode**: Modified domain characters
- **Social Engineering Text**: Attractive document sharing proposal

![Email 6 document sharing scam](https://via.placeholder.com/800x400?text=Email+6+Final+Email)

Selected categories: `Impersonation`, `Typosquatting/Punycodes`, `Social Engineering Text`

**Flag:** `THM{number6-is-the-last-one!-DX!}`

## Flags:

```
Email 1: THM{yougotnumber1-keep-it-going}
Email 2: THM{nmumber2-was-not-tha-thard!}
Email 3: THM{Impersonation-is-aral-thing-keepIt}
Email 4: THM{Get-back-SOC-mas!!}
Email 5: THM{It-was-just-a-sp4m!!}
Email 6: THM{number6-is-the-last-one!-DX!}
```

## Concepts learnt:

* **Phishing vs Spam**: Phishing is a targeted, precision attack aiming to steal credentials or deliver malware; spam is bulk, mostly harmless marketing noise sent to mass audiences.

* **Email Spoofing**: Technique where attackers forge the "From:" field to make emails appear from trusted sources. Detected by checking authentication results (SPF, DKIM, DMARC) and Return-Path in email headers.

* **SPF (Sender Policy Framework)**: Security mechanism that specifies which mail servers are authorized to send emails for a domain. Failed SPF indicates potential spoofing.

* **DKIM (DomainKeys Identified Mail)**: Adds digital signature to emails proving they weren't tampered with and originated from the claimed domain.

* **DMARC (Domain-based Message Authentication)**: Uses SPF and DKIM results to determine email authenticity and what action to take with suspicious emails (quarantine/reject).

* **Typosquatting**: Registering domains with common misspellings of legitimate organizations (e.g., gogle.com instead of google.com) to trick inattentive users.

* **Punycode**: Encoding system converting Unicode characters to ASCII, allowing attackers to register domains using visually similar characters from different alphabets (Cyrillic, Greek, Arabic). Example: "а" (Cyrillic) vs "a" (Latin).

* **Social Engineering in Phishing**: Psychological manipulation exploiting emotions like urgency, fear, curiosity, or helpfulness to trick victims into taking harmful actions.

* **Impersonation**: Attackers pretending to be trusted individuals (executives, IT staff) or legitimate services to gain victim's trust and compliance.

* **Malicious Attachments**: Files (especially .html, .hta, .exe) designed to execute code, steal credentials, or install malware when opened. HTML/HTA files particularly dangerous as they run without browser sandboxing.

* **Fake Login Pages**: Phishing sites mimicking legitimate login portals (Microsoft 365, Google, banking sites) to harvest usernames and passwords. Identifiable by examining URL carefully.

* **Legitimate Service Abuse**: Attackers using trusted platforms (Dropbox, Google Drive, OneDrive) to host malicious content or redirect links, bypassing email security filters since these services appear legitimate.

* **Side Channel Communication**: Tactic where attackers move conversation to platforms outside corporate control (WhatsApp, Telegram, SMS, phone calls) to continue social engineering without detection.

* **Return-Path Header**: Email header revealing the actual sender's email address, often different from the displayed "From:" field in spoofed emails.

## Notes:

* **Key phishing detection workflow**:
  1. Check sender domain - does it match organization's legitimate domain?
  2. Examine email headers - look for failed SPF/DKIM/DMARC
  3. Analyze Return-Path - does it differ from displayed sender?
  4. Look for social engineering tactics in body text
  5. Identify urgency, threats, or too-good-to-be-true offers
  6. Inspect links carefully before clicking (hover to preview URL)
  7. Be suspicious of unexpected attachments
  8. Verify through alternative communication channel if uncertain

* **Modern phishing trends**:
  - Shift from malware delivery to credential theft
  - Abuse of legitimate services to bypass filters
  - Sophisticated social engineering instead of obvious scams
  - Multi-stage attacks using redirects and fake login pages
  - Focus on leaving secure corporate environment

* **Email header analysis tips**:
  - Most email clients hide headers by default
  - Access via "View Source", "Show Original", or "Message Details"
  - Key fields: Authentication-Results, Return-Path, Received, X-Originating-IP
  - Failed authentication (SPF=fail, DKIM=fail, DMARC=fail) = major red flag

* **Punycode detection**:
  - Look for ACE prefix in Return-Path header
  - Characters that look "off" or slightly different
  - Mixed character sets in domain names
  - Browser URL bar may show encoded version (xn--...)

* **Common social engineering phrases**:
  - "URGENT", "IMMEDIATE ACTION REQUIRED"
  - "Your account will be suspended/closed"
  - "Verify your identity immediately"
  - "Unusual activity detected"
  - "Click here to claim your prize/refund"
  - "Limited time offer"
  - "Please do not share this with anyone"

* **Best practices for users**:
  - Never click links in unexpected emails
  - Verify requests through known contact methods
  - Check URLs carefully before entering credentials
  - Report suspicious emails to security team
  - Enable MFA on all accounts
  - Be skeptical of urgent requests, especially financial

* **Corporate defense strategies**:
  - Implement SPF, DKIM, and DMARC records
  - Deploy email filtering and anti-phishing tools
  - Regular security awareness training
  - Phishing simulation exercises
  - Clear reporting mechanisms for suspicious emails
  - Email banner warnings for external senders

## Resources:

* [TryHackMe - Advent of Cyber 2025 Day 12](https://tryhackme.com/r/room/adventofcyber2025)
* [Google Safe Browsing](https://safebrowsing.google.com/)
