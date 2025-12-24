# Advent of Cyber 2025 Day 2 - Merry Clickmas

Learn the types of phishing attacks and explore how red teams create fake login pages using the Social-Engineer Toolkit to send phishing emails and test employee security awareness.

## Solution:

**Step 1: Starting the Environment**

- Started both the AttackBox and the target machine
- Waited approximately 2 minutes for both machines to fully boot

**Step 2: Understanding the Attack Scenario**

- Learned about social engineering - manipulating users to make mistakes like sharing passwords or opening malicious files
- Understood phishing as a subset of social engineering that uses messages (email, SMS, etc.)
- The goal: Test TBFC employees by creating a fake login page to capture credentials
- Learned about the S.T.O.P. mnemonic for identifying phishing attempts:
  - **S**uspicious?
  - **T**elling me to click something?
  - **O**ffering me an amazing deal?
  - **P**ushing me to do something now?

**Step 3: Setting Up the Phishing Trap**

- Navigated to the script directory containing the fake login page

```
root@attackbox:~# cd ~/Rooms/AoC2025/Day02
root@attackbox:~/Rooms/AoC2025/Day02# ./server.py
Starting server on http://0.0.0.0:8000
```

- The script started a web server on port 8000 that would host the fake login page
- Tested the phishing page by browsing to `http://127.0.0.1:8000` in Firefox to see what the victim would see

![Phishing server running](https://github.com/user-attachments/assets/edb63667-9a8a-4ec7-88c0-6b401c9a54ff)

**Step 4: Crafting the Phishing Email with SET**

- Launched the Social-Engineer Toolkit (SET)

```
root@attackbox:~# setoolkit
```

- Selected option **1) Social-Engineering Attacks**
- Then selected option **5) Mass Mailer Attack**
- Chose option **1) E-Mail Attack Single Email Address**

**Step 5: Configuring the Phishing Email**

Filled in the email details to make it convincing:

```
Send email to: factory@wareville.thm
How to deliver the email: 2 (Use your own server or open relay)
From address: updates@flyingdeer.thm
From name: Flying Deer
Username for open-relay: [blank - just hit Enter]
Password for open-relay: [blank - just hit Enter]
SMTP email server address: MACHINE_IP
Port number for the SMTP server: 25 [default]
Flag this message as high priority: no
Do you want to attach a file: n
Do you want to attach an inline file: n
Email subject: Shipping Schedule Changes
Send the message as HTML or plain: p (plain text)
```

**Step 6: Writing the Email Body**

Crafted a convincing message to trick the target:

```
Dear elves,
Kindly note that there have been significant changes to the shipping schedules due to increased shipping orders.
Please confirm the new schedule by visiting http://CONNECTION_IP:8000
Best regards,
Flying Deer
END
```

- The email was sent successfully
- SET displayed: `[*] SET has finished sending the emails`

**Step 7: Capturing Credentials**

- Returned to the terminal running `server.py`
- Waited 1-2 minutes for the victim to fall for the trap
- Successfully captured the credentials when the user entered them on the fake login page

```
Password captured: unranked-wisdom-anthem
```

**Step 8: Testing Credential Reuse**

- Browsed to `http://MACHINE_IP` to access the TBFC email portal
- Attempted to login with:
  - Username: `factory`
  - Password: `unranked-wisdom-anthem`
- Successfully gained access! The admin had reused their password
- Found emails in the factory user's mailbox
- Discovered the total number of toys expected for delivery: **1,984,000**

## Flags:

```
Password: unranked-wisdom-anthem
Total toys for delivery: 1984000
```

## Concepts learnt:

- **Social Engineering**: Manipulating humans (not computer systems) to make mistakes by exploiting psychological factors like urgency, curiosity, and authority
- **Phishing Types**: Email phishing, smishing (SMS), vishing (voice calls), quishing (QR codes), and social media direct messages
- **Social-Engineer Toolkit (SET)**: An open-source tool for conducting authorized social engineering attacks and penetration testing
- **Credential Harvesting**: Creating fake login pages to capture user credentials when they attempt to authenticate
- **Email Spoofing**: Making phishing emails appear to come from legitimate, trusted sources
- **SMTP (Simple Mail Transfer Protocol)**: The protocol used for sending emails, operating on port 25 by default
- **Password Reuse Vulnerability**: When users reuse the same password across multiple systems, compromising one account can lead to access across multiple platforms
- **Red Team Operations**: Authorized security testing where ethical hackers simulate real attacks to identify vulnerabilities

## Notes:

- The exercise emphasized the importance of making phishing emails look realistic - using trusted sender names (Flying Deer shipping company) and relevant subject lines
- The attack was more effective because it leveraged an existing trust relationship (factory elves regularly communicate with the shipping company)
- An alternative approach could have been to create a high-priority flagged email to create more urgency
- Could have also attached malicious files instead of using a fake login page
- The server script (`server.py`) automatically captured and displayed credentials without requiring manual log checking
- It took 1-2 minutes for the victim to respond to the phishing email - important to be patient during real penetration tests
- The successful credential reuse demonstrates why organizations need strong password policies that prevent reusing passwords across systems
- This was an authorized penetration test - conducting phishing attacks without permission is illegal

## Resources:

- (https://github.com/trustedsec/social-engineer-toolkit)
- (https://tryhackme.com/room/phishingprevention)
- (https://www.allthingssecured.com/)
- [TryHackMe Advent of Cyber 2025](https://tryhackme.com/christmas)

***
