# YARA mean one!

Learn how to use YARA rules to identify and classify malware by searching for unique patterns, create custom detection rules, and decode hidden messages within files using pattern matching and regular expressions.

## Solution:

**Step 1: Starting the Environment**

- Started the target VM and waited approximately 2 minutes for full boot
- Accessed the machine with pre-configured environment
- Target directory: `/home/ubuntu/Downloads/easter` containing images from McSkidy
- Mission: Find hidden message using YARA rules to detect keyword patterns

**Step 2: Understanding YARA Fundamentals**

**What is YARA?**
- Tool for identifying and classifying malware by searching for unique patterns
- Acts as "digital fingerprints" detector for attackers' traces
- Allows defenders to define their own rules for what constitutes malicious behavior
- Not dependent on antivirus updates or third-party alerts

**Why Use YARA?**

**Common Use Cases:**
- **Post-incident analysis**: Verify if malware traces exist across other systems
- **Threat hunting**: Search endpoints for known malware families
- **Intelligence-based scans**: Apply shared rules to detect new IOCs
- **Memory analysis**: Examine active processes for malicious code fragments

**Key Advantages:**
- **Speed**: Quickly scans large sets of files
- **Flexibility**: Detects text strings, binary patterns, and complex logic
- **Control**: Analysts define exactly what's considered malicious
- **Shareability**: Rules can be reused and improved by community
- **Visibility**: Connects scattered clues into clear attack pictures

**Step 3: Understanding YARA Rule Structure**

**Three Key Elements:**

1. **Metadata**: Information about the rule (author, description, date)
2. **Strings**: Clues YARA searches for (text, bytes, regex)
3. **Conditions**: Logic determining when rule triggers

**Example Rule Structure:**
```
rule TBFC_KingMalhare_Trace
{
    meta:
        author = "Defender of SOC-mas"
        description = "Detects traces of King Malhare's malware"
        date = "2025-10-10"
    
    strings:
        $s1 = "rundll32.exe" fullword ascii
        $s2 = "msvcrt.dll" fullword wide
        $url1 = /http:\/\/.*malhare.*/ nocase
    
    condition:
        any of them
}
```

**Step 4: Learning String Types**

**1. Text Strings:**
- Simplest type representing words or text fragments
- Default: ASCII and case-sensitive

**Modifiers:**
- `nocase`: Case-insensitive matching
- `wide`: Search for Unicode (two-byte) characters
- `ascii`: Single-byte search
- `xor`: Automatically checks XOR-encoded variations
- `base64`: Decodes and searches Base64-encoded content

**Example:**
```
strings:
    $xmas = "Christmas" nocase wide ascii
    $hidden = "Malhare" xor
    $b64 = "SOC-mas" base64
```

**2. Hexadecimal Strings:**
- Search for specific byte patterns
- Useful for file headers, shellcode, binary signatures

**Example:**
```
strings:
    $mz = { 4D 5A 90 00 }  // MZ header of Windows executable
    $hex = { E3 41 ?? C8 }  // ?? = wildcard byte
```

**3. Regular Expression Strings:**
- Flexible patterns matching multiple variations
- Useful for URLs, encoded commands, dynamic filenames

**Example:**
```
strings:
    $url = /http:\/\/.*malhare.*/ nocase
    $cmd = /powershell.*-enc\s+[A-Za-z0-9+/=]+/ nocase
```

**Step 5: Understanding Conditions**

**Condition Types:**

**Single String Match:**
```
condition:
    $xmas
```

**Any String Match:**
```
condition:
    any of them
```

**All Strings Match:**
```
condition:
    all of them
```

**Logical Operators:**
```
condition:
    ($s1 or $s2) and not $benign
```

**File Property Checks:**
```
condition:
    any of them and (filesize < 700KB)
```

**Step 6: Studying Real-World Example**

**IcedID Trojan Detection Rule:**

```yara
rule TBFC_Simple_MZ_Detect
{
    meta:
        author = "TBFC SOC L2"
        description = "IcedID Rule"
        date = "2025-10-10"
        confidence = "low"
    
    strings:
        $mz   = { 4D 5A }                     // MZ header (PE file)
        $hex1 = { 48 8B ?? ?? 48 89 }         // malicious binary fragment
        $s1   = "malhare" nocase              // IOC string
    
    condition:
        all of them and filesize < 10485760  // < 10MB
}
```

**Executing YARA:**
```
yara -r icedid_starter.yar C:\
```

**Useful Flags:**
- `-r`: Scan directories recursively
- `-s`: Print matching strings found in files

**Step 7: Creating Custom YARA Rule**

**Mission Requirements:**
- Search for keyword `TBFC:` followed by alphanumeric characters
- Scan directory: `/home/ubuntu/Downloads/easter`
- Extract message sent by McSkidy

**Creating the Rule File:**

```bash
nano tbfc_search.yar
```

**Initial YARA Rule (Simple String Match):**
```
rule TBFC_string
{
    strings:
        $TBFC = "TBFC"
    
    condition:
        all of them
}
```

**Step 8: Scanning for Simple TBFC String**

**Executing First Scan:**
```
yara -r tbfc_search.yar /home/ubuntu/Downloads/easter
```

**Results:**
- **5 images contained the string "TBFC"**
- Files identified but need more specific pattern

**Step 9: Creating Advanced Regex Rule**

**Understanding the Requirement:**
- Pattern: `TBFC:` followed by one or more alphanumeric characters
- Need regex to capture: `TBFC:`, then `[A-Za-z0-9]+` (one or more alphanumeric)

**Regex Pattern:**
```
/TBFC:[A-Za-z0-9]+/
```

**Updated YARA Rule:**
```
rule TBFC_Message_Decoder
{
    meta:
        author = "TBFC Blue Team"
        description = "Extracts hidden McSkidy messages"
        date = "2025-12-13"
    
    strings:
        $pattern = /TBFC:[A-Za-z0-9]+/
    
    condition:
        $pattern
}
```

**Step 10: Executing Advanced Scan**

```
yara -rs tbfc_search.yar /home/ubuntu/Downloads/easter
```

**Flags Used:**
- `-r`: Recursive scanning
- `-s`: Show matching strings

**Step 11: Analyzing Suspicious Files**

- Noticed unusual file: `embeds` (no image extension)
- Examined file contents directly

```bash
cat /home/ubuntu/Downloads/easter/embeds
```

**File Contents:**
```
TBFC:Find
TBFC:me
TBFC:in
TBFC:HopSec
TBFC:Island
```

**Step 12: Decoding the Message**

- Extracted keywords in order:
  1. Find
  2. me
  3. in
  4. HopSec
  5. Island

- **Decoded Message: "Find me in HopSec Island"**
- McSkidy's location revealed!

## Flags:

```
Images containing "TBFC": 5
Regex pattern: /TBFC:[A-Za-z0-9]+/
McSkidy's message: Find me in HopSec Island
```

## Concepts learnt:

- **YARA (Yet Another Recursive Acronym)**: Pattern-matching tool for identifying and classifying malware based on textual or binary patterns
- **Malware Fingerprinting**: Process of identifying unique characteristics (strings, bytes, behaviors) that distinguish malicious code
- **String Matching**: Searching for specific text sequences within files - foundation of pattern-based detection
- **Hexadecimal Strings**: Byte-level patterns represented in hex notation (e.g., `{ 4D 5A }` for MZ header)
- **Regular Expressions (Regex)**: Flexible pattern-matching syntax for finding text that follows specific structures or formats
- **Case-Insensitive Matching**: Using `nocase` modifier to match strings regardless of letter casing
- **Wide Character Strings**: Unicode (two-byte) characters used in Windows executables - detected with `wide` modifier
- **XOR Encoding**: Simple obfuscation technique using XOR operation - YARA's `xor` modifier checks all single-byte variations
- **Base64 Encoding**: Common encoding scheme for hiding payloads - YARA can decode and search with `base64` modifier
- **Obfuscation**: Techniques attackers use to hide malicious code from detection (encoding, encryption, case changes)
- **File Header**: Beginning bytes of a file indicating its type (e.g., `4D 5A` = MZ header for Windows executables)
- **Shellcode**: Small piece of code used as payload in exploitation - often detected via hex patterns
- **Logical Operators**: Boolean logic (`and`, `or`, `not`) combining multiple conditions into complex detection rules
- **Wildcard Bytes**: `??` in hex strings represents any byte value - useful for matching patterns with variable sections
- **Recursive Scanning**: `-r` flag makes YARA traverse subdirectories automatically
- **Threat Intelligence**: Shared knowledge about malware characteristics (IOCs, patterns, behaviors) used to strengthen defenses
- **IOC (Indicator of Compromise)**: Observable artifact or pattern indicating potential security breach
- **False Positives**: Legitimate files incorrectly flagged as malicious - reduced by stricter conditions

## Concepts learnt:

- **YARA (Yet Another Recursive Acronym)**: Pattern-matching tool for identifying and classifying malware based on textual or binary patterns
- **Malware Fingerprinting**: Process of identifying unique characteristics (strings, bytes, behaviors) that distinguish malicious code
- **String Matching**: Searching for specific text sequences within files - foundation of pattern-based detection
- **Hexadecimal Strings**: Byte-level patterns represented in hex notation (e.g., `{ 4D 5A }` for MZ header)
- **Regular Expressions (Regex)**: Flexible pattern-matching syntax for finding text that follows specific structures or formats
- **Case-Insensitive Matching**: Using `nocase` modifier to match strings regardless of letter casing
- **Wide Character Strings**: Unicode (two-byte) characters used in Windows executables - detected with `wide` modifier
- **XOR Encoding**: Simple obfuscation technique using XOR operation - YARA's `xor` modifier checks all single-byte variations
- **Base64 Encoding**: Common encoding scheme for hiding payloads - YARA can decode and search with `base64` modifier
- **Obfuscation**: Techniques attackers use to hide malicious code from detection (encoding, encryption, case changes)
- **File Header**: Beginning bytes of a file indicating its type (e.g., `4D 5A` = MZ header for Windows executables)
- **Shellcode**: Small piece of code used as payload in exploitation - often detected via hex patterns
- **Logical Operators**: Boolean logic (`and`, `or`, `not`) combining multiple conditions into complex detection rules
- **Wildcard Bytes**: `??` in hex strings represents any byte value - useful for matching patterns with variable sections
- **Recursive Scanning**: `-r` flag makes YARA traverse subdirectories automatically
- **Threat Intelligence**: Shared knowledge about malware characteristics (IOCs, patterns, behaviors) used to strengthen defenses
- **IOC (Indicator of Compromise)**: Observable artifact or pattern indicating potential security breach
- **False Positives**: Legitimate files incorrectly flagged as malicious - reduced by stricter conditions

## Notes:

- YARA rules are saved with `.yar` extension by convention
- Metadata fields (`author`, `description`, `date`) are optional but highly recommended for rule management
- The `fullword` modifier ensures string appears as complete word, not part of larger string
- Can use `$` prefix for string variables (e.g., `$s1`, `$url1`) - helps organize multiple strings
- Alternative to `any of them`: Can specify `any of ($s*)` to match any string starting with `$s`
- The `man yara` command provides comprehensive documentation on all flags and options
- YARA can scan files, directories, processes, and memory dumps
- Could have used `yara -rs` to see exact matching strings in output - useful for debugging rules
- The `embeds` file had no extension - suspicious indicator worth investigating
- Alternative approach: Could have written script to automatically extract and sort keywords
- Regex `[A-Za-z0-9]` could be simplified to `\w` for word characters (but less explicit)
- Performance consideration: Broad regex patterns can slow down scans - be specific when possible
- Real-world usage: YARA rules often shared via GitHub repositories (e.g., Yara-Rules project)
- Can integrate YARA with SIEM systems, threat intelligence platforms, and malware sandboxes
  
## Resources:

- [YARA Official Documentation](https://yara.readthedocs.io/)
- [YARA Rules GitHub Repository](https://github.com/Yara-Rules/rules)

***
