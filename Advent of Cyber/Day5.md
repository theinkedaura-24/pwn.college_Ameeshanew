# Santa's Little IDOR

Learn how to spot and exploit Insecure Direct Object References (IDOR) vulnerabilities, understand authentication vs authorization, and discover how to fix IDOR issues to prevent unauthorized data access.

## Solution:

**Step 1: Starting the Environment**

- Started both the AttackBox and target machine
- Waited approximately 2 minutes for machines to fully boot
- Navigated to the TryPresentMe application at `http://MACHINE_IP` in the browser

**Step 2: Understanding IDOR Fundamentals**

**What is IDOR?**
- IDOR stands for **Insecure Direct Object Reference**
- An access control vulnerability where web applications use references (like IDs) without proper authorization checks
- Example: Changing `packageID=1001` to `packageID=1002` in a URL to view someone else's package

**Key Concepts Learned:**

**Authentication vs Authorization:**
- **Authentication**: Verifying who you are (username/password, session tokens)
- **Authorization**: Verifying what you're allowed to do (permissions check)
- Authorization cannot happen before authentication - system must know who you are first

**Privilege Escalation Types:**
- **Vertical Privilege Escalation**: Gaining access to more features (e.g., normal user performing admin actions)
- **Horizontal Privilege Escalation**: Using authorized features but accessing unauthorized data (e.g., viewing other users' accounts)
- **IDOR is typically horizontal privilege escalation**

**Step 3: Logging into TryPresentMe**

- Logged into the application using provided credentials
- Accessed the dashboard showing user profile, children, and vouchers

**Step 4: Discovering Simple IDOR via Developer Tools**

- Right-clicked on the page and selected **Inspect**
- Navigated to the **Network** tab in Developer Tools
- Refreshed the page to capture network requests

![Developer Tools Network tab](https://github.com/user-attachments/assets/example-network-tab)

**Step 5: Analyzing the view_accountinfo Request**

- Clicked on the `view_accountinfo` request
- Examined the request headers and found `user_id=10` parameter
- Clicked the **Response** tab to see user data corresponding to user_id 10
- **Discovery**: Application uses user_id as direct reference without proper authorization

**Step 6: Exploiting Simple IDOR in Local Storage**

- Navigated to **Storage** tab in Developer Tools
- Expanded **Local Storage** dropdown on the left
- Clicked on the URL to view stored data
- Found `auth_user` entry containing `user_id: 10`

**Exploitation Steps:**
```
1. Double-clicked on the Value field of auth_user
2. Changed user_id from 10 to 11
3. Pressed Enter to save
4. Refreshed the page
```

- **Result**: Successfully accessed another user's dashboard and data!
- This demonstrated the simplest form of IDOR - just changing a number

**Step 7: Finding IDOR in Encoded Values (Base64)**

- Clicked the eye icon next to the first child to view child details
- Returned to **Network** tab to examine the request
- Found request containing `Mg==` parameter

**Analysis:**
- `Mg==` is Base64 encoded
- Decoded value: `2` (the child ID)
- **IDOR still possible** - just need to Base64 encode numbers before making requests

**Step 8: Discovering IDOR in Hashed Values (MD5)**

- Clicked the edit icon next to a child
- Examined the API request in Network tab
- Found MD5 hash being used as child identifier

**Key Insight:**
- While hash looks random, it's deterministic
- If we know what value was hashed, we can replicate the hash
- Used hash identifier tools to confirm MD5 algorithm
- **IDOR possible** by replicating the hashing function

**Step 9: Understanding UUID v1 Vulnerability**

- Examined voucher codes in the application
- Vouchers formatted as UUIDs
- Used [UUID Decoder](https://www.uuidtools.com/) to analyze voucher format

**UUID v1 Weakness:**
- Contains timestamp information
- If we know when voucher was generated, we can recreate it
- Example: Vouchers generated between 20:00-21:00 = only 3600 possible UUIDs
- **Vulnerable to brute force attacks**

**Step 10: Finding the Parent with 10 Children**

- Changed `user_id` in Local Storage systematically
- Method: Trial and error, incrementing user_id one at a time

```
user_id=10 → Check children count
user_id=11 → Check children count
user_id=12 → Check children count
...
user_id=15 → Found 10 children!
```

- **Answer: User ID 15 has 10 children**

**Step 11: Understanding IDOR Prevention**

**Best Practices Learned:**
1. **Server-side permission checks**: Always verify user authorization for every request
2. **Don't rely on obfuscation**: Base64 encoding or hashing IDs doesn't fix IDOR
3. **Session validation**: Check "Does this user own/have permission to view this item?"
4. **Use random IDs for public links**: But remember - random IDs alone aren't enough
5. **Monitor failed access attempts**: Early signs of exploitation attempts
6. **Test thoroughly**: Try accessing other users' data to ensure it's blocked

**The Fix: SDOR (Secure Direct Object Reference)**
- Implement proper authorization checks before returning data
- Verify logged-in user has permission to access requested resource
- Keep permission logic server-side, never client-side

## Flags:

```
IDOR stands for: Insecure Direct Object Reference
Type of privilege escalation: Horizontal
Parent with 10 children: User ID 15
```

## Concepts learnt:

- **IDOR (Insecure Direct Object Reference)**: Access control vulnerability where applications use direct references without authorization checks, allowing unauthorized data access
- **Authentication**: Process of verifying user identity through credentials or session tokens - happens on every request, not just login
- **Authorization**: Process of verifying user permissions - checking what actions/data a user is allowed to access
- **Session Information**: Cookies or tokens provided after authentication, included in subsequent requests for continuous authentication
- **Horizontal Privilege Escalation**: Using authorized features to access unauthorized data at the same privilege level (viewing other users' accounts)
- **Vertical Privilege Escalation**: Gaining access to higher-privilege features (normal user performing admin actions)
- **Base64 Encoding**: Encoding scheme that converts binary data to ASCII text - easily reversible, not encryption or security measure
- **MD5 Hashing**: One-way hashing algorithm that creates fixed-length hash from input - deterministic, so same input always produces same hash
- **UUID (Universally Unique Identifier)**: 128-bit identifier with different versions - version 1 includes timestamp, making it predictable
- **UUID v1 Vulnerability**: Contains MAC address and timestamp, allowing attackers to regenerate UUIDs if generation time is known
- **Direct Object Reference**: Using identifiers (IDs, hashes, UUIDs) to point directly to database objects or resources
- **Authorization Bypass**: Alternative and more accurate term for IDOR - focuses on missing authorization checks rather than reference type
- **Developer Tools**: Browser built-in debugging tools for inspecting network requests, local storage, cookies, and page elements
- **Local Storage**: Browser-side data storage mechanism - accessible and modifiable by users, should never be trusted for security
- **Brute Force Attack**: Systematically trying all possible combinations to find valid credentials, IDs, or other sensitive values

## Notes:

- The vulnerability name "IDOR" is somewhat misleading - it's really an **Authorization Bypass** issue, not about the reference type itself
- Hiding or encoding IDs (Base64, MD5, UUIDs) doesn't fix IDOR if authorization checks are missing
- **Security through obscurity doesn't work** - must implement proper server-side permission checks
- Authentication happens on every request via session tokens, not just at login
- Local Storage is client-side and completely under user control - never trust it for security decisions
- Alternative attack method for finding user with 10 children: Could have scripted automated requests to check all user IDs
- Bonus challenge mentioned using Burp Suite's Intruder for automated iteration - professional penetration testing tool
- The Base64 child endpoint could be exploited by encoding sequential numbers: `base64_encode(1)`, `base64_encode(2)`, etc.
- MD5 child endpoint exploitation would require knowing what values were hashed (likely sequential IDs)
- UUID v1 voucher attack would require generating 14,400 UUIDs (4 hours × 3600 seconds/hour) for the 20:00-24:00 UTC window
- Real-world IDOR vulnerabilities are extremely common - often in APIs, dashboards, and data retrieval endpoints
- Always test authorization by attempting to access resources belonging to other users during security assessments
- Proper fix requires implementing authorization middleware that validates user permissions before processing requests
- The TryPresentMe application intentionally contained multiple IDOR examples for educational purposes

## Resources:

- (https://tryhackme.com/room/idor)
- [UUID Decoder Tool](https://www.uuidtools.com/)
- [Base64 Encoder/Decoder](https://www.base64decode.org/)
- [Hash Identifier Tool](https://hashes.com/en/tools/hash_identifier)
- [Burp Suite Intruder Documentation](https://portswigger.net/burp/documentation/desktop/tools/intruder)

***
