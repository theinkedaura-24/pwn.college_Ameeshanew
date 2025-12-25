# Hoperation Save McSkidy

Use CyberChef to decode layered encodings and break through five security locks to rescue McSkidy from King Malhare's Quantum Warren fortress.

## Solution:

**Step 1: Understanding Core Concepts**

**Encoding vs Encryption:**

| Aspect | Encoding | Encryption |
|--------|----------|------------|
| Purpose | Compatibility & Usability | Security & Confidentiality |
| Process | Standardized algorithm | Algorithm + Secret Key |
| Security | No security | Provides security |
| Reversibility | Easy to reverse | Hard without key |
| Speed | Fast | Slower |
| Examples | Base64, URL encoding | AES, RSA, TLS |

**CyberChef Overview:**

![CyberChef interface](https://via.placeholder.com/800x400?text=CyberChef+Interface)

- **Operations**: Repository of encoding/decoding/cipher operations
- **Recipe**: Chain multiple operations together
- **Input**: Raw data to process
- **Output**: Transformed result

**Step 2: Setting Up**

- Started AttackBox and target machine
- Navigated to web application at `http://MACHINE_IP:8080`
- Opened CyberChef (offline version from AttackBox bookmarks or online)
- Opened browser Developer Tools (F12 or right-click → Inspect)

**Key investigation areas:**
1. **Network tab**: Headers containing hints/questions
2. **Debugger tab**: JavaScript login logic for each lock
3. **Chat panel**: Communicate with guards (Base64 encoded)
4. **Login form**: Guard name hints

**Step 3: First Lock - Outer Gate**

**Investigation:**

1. **Identified guard name** from login form hint
2. **Checked Network tab** (refresh page first):
   - Selected first HTTP response
   - Found magic question in headers: "What is the password for this level?"

![Network tab showing headers](https://via.placeholder.com/800x400?text=Network+Tab+Headers)

3. **Encoded the magic question** in CyberChef using To Base64
4. **Sent encoded question** to guard via chat panel
5. **Received encoded response** from guard

**Decoding process:**

```
Guard's encoded response → From Base64 → Plaintext password
```

6. **Checked Debugger tab** for login logic:
   - Password simply encoded to Base64
   - Username must be Base64-encoded guard name

**CyberChef recipe:**
```
From Base64
```

![First lock CyberChef decoding](https://via.placeholder.com/800x400?text=First+Lock+Decoding)

7. **Login credentials:**
   - Username: Base64-encoded guard name
   - Password: `Iamsofluffy` (decoded from guard's response)

**Password**: `Iamsofluffy`

---

**Step 4: Second Lock - Outer Wall**

**Investigation:**

1. Identified new guard name
2. Extracted magic question from headers: "Did you change the password?"
3. Encoded question and sent to guard
4. Received encoded response

**Key difference**: Login logic applies **double Base64 encoding**

**Checked Debugger tab:**
```
// Password encoded TWICE with Base64
password = btoa(btoa(actualPassword));
```

**CyberChef recipe:**
```
From Base64
From Base64  // Second decoding step
```

![Second lock double Base64 decoding](https://via.placeholder.com/800x400?text=Double+Base64+Decoding)

**Login:**
- Username: Base64-encoded guard name
- Password: `Itoldyoutochangeit!` (decoded twice)

**Password**: `Itoldyoutochangeit!`

---

**Step 5: Third Lock - Guard House**

**Investigation:**

**Important note**: No magic question from this lock onwards - just ask politely!

1. Identified guard name
2. Found XOR key in headers: `cyberchef`
3. Sent simple message: `Password please.` (encoded in Base64)
4. Guard took ~2-3 minutes to respond (may be sleeping!)

**Understanding XOR:**

XOR (Exclusive OR) is a bitwise operation:
- Input: Data + Key
- Property: XOR(XOR(data, key), key) = data
- **Reversible**: XOR the result with the same key to get original data

![XOR logic diagram](https://via.placeholder.com/800x400?text=XOR+Logic)

**Login logic from Debugger:**
```
// Password XOR'd with key, then Base64 encoded
password = btoa(XOR(actualPassword, "cyberchef"));
```

**Reverse process:**
```
Encoded response → From Base64 → XOR with "cyberchef" → Plaintext
```

**CyberChef recipe:**
```
From Base64
XOR
  Key: cyberchef
  Key format: UTF-8
```

![Third lock XOR decoding](https://via.placeholder.com/800x400?text=XOR+Decoding)

**Login:**
- Username: Base64-encoded guard name
- Password: `BugsBunny`

**Password**: `BugsBunny`

---

**Step 6: Fourth Lock - Inner Castle**

**Investigation:**

**No header information needed for this lock**

1. Identified guard name
2. Asked: `Password please.` (Base64 encoded)
3. Received odd-looking encoded response

**Decoded response revealed**: MD5 hash string

**Understanding MD5:**

MD5 (Message-Digest Algorithm 5):
- Produces 128-bit (32 hex characters) hash
- One-way cryptographic function (cannot reverse mathematically)
- Can be cracked using precomputed hash databases

**Login logic:**
```
// Password converted to MD5 hash
password = MD5(actualPassword);
```

**Cracking the hash:**

1. Decoded guard's Base64 response to get MD5 hash
2. Opened [CrackStation](https://crackstation.net/)
3. Pasted the MD5 hash
4. CrackStation returned plaintext password from its database

![CrackStation MD5 hash lookup](https://via.placeholder.com/800x400?text=CrackStation+Result)

**Login:**
- Username: Base64-encoded guard name
- Password: `Passw0rd1`

**Password**: `Passw0rd1`

---

**Step 7: Fifth Lock - Prison Tower**

**McSkidy's warning**: "Different mechanisms change occasionally. Match the correct approach when decoding."

**Investigation:**

1. Identified guard name
2. **Critical**: Found **Recipe ID** in headers (Network tab)
3. Asked guard for password
4. Received encoded response

**Dynamic login logic** - depends on Recipe ID:

| Recipe ID | Reverse Decoding Steps |
|-----------|----------------------|
| 1 | From Base64 → Reverse → ROT13 |
| 2 | From Base64 → From Hex → Reverse |
| 3 | ROT13 → From Base64 → XOR (key from headers) |
| 4 | ROT13 → From Base64 → ROT47 |

**My instance had Recipe ID: 4**

**Understanding ROT ciphers:**
- **ROT13**: Rotates letters 13 positions in alphabet (A→N, B→O)
- **ROT47**: Rotates ASCII printable characters 47 positions

**CyberChef recipe for Recipe 4:**
```
ROT13
From Base64
ROT47
```

![Fifth lock multi-cipher decoding](https://via.placeholder.com/800x400?text=Recipe+4+Decoding)

**Process:**
1. Took encoded response from guard
2. Applied ROT13 first
3. Then From Base64
4. Finally ROT47
5. Got plaintext password: `51rBr34chBl0ck3r`

**Login:**
- Username: Base64-encoded guard name
- Password: `51rBr34chBl0ck3r`

**After successful login**: Retrieved flag!

![McSkidy escaping the fortress](https://via.placeholder.com/800x400?text=Mission+Complete)

**Password**: `51rBr34chBl0ck3r`

## Flags:

```
First Lock: Iamsofluffy
Second Lock: Itoldyoutochangeit!
Third Lock: BugsBunny
Fourth Lock: Passw0rd1
Fifth Lock: 51rBr34chBl0ck3r
Final Flag: THM{M3D13V4L_D3C0D3R_4D3P7}
```

## Concepts learnt:

* **Encoding**: Transforming data for compatibility between systems using standardized algorithms. NOT for security - anyone can decode. Examples: Base64, URL encoding, hex encoding. Used for transmitting binary data over text-based protocols.

* **CyberChef**: Open-source web tool (Cyber Swiss Army Knife) for encoding, decoding, encryption, decryption, data analysis, and transformation. Allows chaining multiple operations ("recipes") to process complex data transformations.

* **Base64 Encoding**: Converts binary data to ASCII text using 64 printable characters (A-Z, a-z, 0-9, +, /). Increases data size by ~33%. Commonly used for embedding images in HTML, email attachments, and API data transfer.

* **XOR (Exclusive OR)**: Bitwise operation comparing two bits - returns 1 if bits differ, 0 if same. Key property: XOR is its own inverse (applying XOR twice with same key returns original). Used in encryption, obfuscation, and simple ciphers.

* **ROT13 (Rotate 13)**: Simple letter substitution cipher rotating alphabet 13 positions. A↔N, B↔O, etc. Self-inverse (applying ROT13 twice returns original). Not secure - only obscures text.

* **ROT47**: Extension of ROT13 for all printable ASCII characters (33-126). Rotates 47 positions. Includes numbers and symbols, not just letters.

* **MD5 (Message-Digest Algorithm 5)**: Cryptographic hash function producing 128-bit (32 hex character) hash. One-way function - cannot reverse mathematically. Vulnerable to collisions. Used for checksums, not recommended for security.

* **Hash Cracking**: Using precomputed databases (rainbow tables) to reverse hashes by looking up hash→plaintext mappings. Services like CrackStation maintain billions of precomputed hashes.

* **HTTP Headers**: Metadata sent with HTTP requests/responses containing information about the request, server, content type, cookies, custom application data. Accessible via browser Developer Tools Network tab.

* **JavaScript Debugging**: Inspecting client-side code execution logic using browser DevTools Debugger tab. Reveals application logic, validation rules, and security mechanisms implemented on client side.

* **Operation Chaining**: Combining multiple transformations in sequence where output of one operation becomes input of next. CyberChef excels at this - critical for reversing complex encoding schemes.

## Notes:

* **CyberChef operation tips**:
  - Drag operations from left panel to middle Recipe area
  - Toggle operations on/off using middle button
  - "Bake" button executes recipe (or enable Auto Bake)
  - Use "Output" as input for next operation (chain)
  - Save useful recipes for later reuse
  - Can load/save recipes as JSON

* **Common encoding patterns encountered**:
  - Single Base64 (Lock 1)
  - Double Base64 (Lock 2)
  - XOR + Base64 (Lock 3)
  - MD5 hashing (Lock 4)
  - Multi-cipher combinations (Lock 5)

* **Guard communication strategy**:
  - Always encode messages in Base64 before sending
  - Keep messages short: "Password please." works well
  - Guards may take 2-3 minutes to respond (especially Lock 3+)
  - Patient waiting required - don't spam requests

* **Browser DevTools investigation workflow**:
  1. **Network tab**: Find headers with hints/questions
  2. **Debugger tab**: Examine JavaScript for login logic
  3. **Console tab**: Can manually test encoding functions
  4. Refresh page in Network tab to capture requests

* **Reverse engineering login logic**:
  - Login checks happen client-side (JavaScript)
  - Logic reveals encoding steps in order
  - Reverse the operations in opposite order
  - Example: If logic is `XOR → Base64`, reverse is `From Base64 → XOR`

* **Why each technique is used**:
  - **Base64**: Most common encoding, easy to transmit
  - **Double Base64**: Adds complexity, harder to spot patterns
  - **XOR**: Simple encryption-like obfuscation with key
  - **MD5**: Hash-based verification (irreversible without cracking)
  - **ROT ciphers**: Simple substitution, obscures plaintext
  - **Multiple layers**: Defense in depth, requires multiple steps

* **Hash cracking resources**:
  - **CrackStation**: Free, huge database, good for common passwords
  - **Hashcat**: Local GPU-accelerated cracking tool
  - **John the Ripper**: Another powerful local cracking tool
  - **Online rainbow tables**: Various websites maintain hash databases

* **Real-world applications**:
  - Malware analysts reverse obfuscated commands
  - SOC analysts decode suspicious Base64 in logs
  - Penetration testers identify weak encoding/hashing
  - Incident responders decrypt attacker communications
  - CTF challenges test encoding/decoding skills

* **Security implications**:
  - Encoding ≠ Security - Base64 is trivially decoded
  - Client-side validation can be bypassed
  - Simple XOR with known key provides no real security
  - MD5 vulnerable to rainbow table attacks
  - ROT ciphers are toy ciphers, never use for real security
  - Never rely on "security through obscurity"

* **Alternative tools**:
  - **CyberChef** (best for recipes): https://gchq.github.io/CyberChef/
  - **Base64 Decode**: https://www.base64decode.org/
  - **CrackStation**: https://crackstation.net/
  - **dCode**: https://www.dcode.fr/ (cipher identification)
  - Python scripts with `base64`, `hashlib` modules
  - Linux command line: `base64`, `openssl`, `xxd`

## Resources:

* [TryHackMe - Advent of Cyber 2025 Day 17](https://tryhackme.com/r/room/adventofcyber2025)
* [TryHackMe - Introduction to Cryptography Room](https://tryhackme.com/room/cryptographyintro)
* [Base64 Encoding Explained](https://en.wikipedia.org/wiki/Base64)
* [XOR Cipher Explanation](https://en.wikipedia.org/wiki/XOR_cipher)
* [MD5 Hash Function](https://en.wikipedia.org/wiki/MD5)
* [CrackStation Hash Cracking](https://crackstation.net/)
* [ROT13 Cipher Tool](https://rot13.com/)
* [dCode Cipher Identifier](https://www.dcode.fr/cipher-identifier)
