# Obfuscation: The Egg Shell File

WareVille is under attack! McSkidy discovers a suspicious email from "northpole-hr" (which doesn't exist - TBFC's HR is at the South Pole). A tiny PowerShell file was downloaded containing gibberish strings of random characters. Time to learn about obfuscation and decipher the hidden malicious code.

## Solution:

### Understanding Obfuscation Techniques

- **Obfuscation** is the practice of making data hard to read and analyze - attackers use it to evade detection
- Different from encoding (for compatibility) and encryption (for security) - obfuscation is meant to slow down analysis
- I learned about common obfuscation patterns:
  - **ROT1/ROT13**: Letter shifting (e.g., `carrot` → `dbsspu`)
  - **Base64**: Long alphanumeric strings, often ending in `=` or `==`
  - **XOR**: Random symbols but same length as original
  - **Layered obfuscation**: Multiple techniques combined

![CyberChef Interface](https://i.imgur.com/example1.png)

### Part 1: Deobfuscating the C2 URL

- Opened `SantaStealer.ps1` from Desktop in Visual Studio
- Located the "Start here" section with Base64-encoded string
- Used CyberChef's "From Base64" operation to decode the obfuscated C2 URL

```
# Original obfuscated string in script
$c2Url = "aHR0cHM6Ly9jMi5leGFtcGxlLmNvbS9hcGk="

# Decoded using CyberChef
# Result: https://c2.example.com/api
```

- Replaced the obfuscated string with decoded URL in the script
- Ran the script from PowerShell:

```
PS C:\Users\Administrator> cd .\Desktop\
PS C:\Users\Administrator\Desktop> .\SantaStealer.ps1
```

![First Flag Output](https://i.imgur.com/example2.png)

### Part 2: Obfuscating the API Key

- Found the malicious actor's API key in Part 2 of the script
- Used CyberChef's XOR operation with the specified key to obfuscate it
- Settings: Added XOR operation, set Key value, ensured dropdown was set to HEX
- Clicked "BAKE!" to get the obfuscated result

```
Original API Key: [redacted]
XOR Key: a (HEX)
Obfuscated Result: [garbled characters]
```

- Replaced the API key in script with obfuscated version
- Ran the script again to retrieve the second flag

```
PS C:\Users\Administrator\Desktop> .\SantaStealer.ps1
```

## Flags:

```
THM{C2_De0bfuscation_29838}
THM{API_Obfusc4tion_ftw_0283}
```

## Concepts learnt:

* **Obfuscation vs Encoding vs Encryption**: Obfuscation hides intent (not secure), encoding changes format (easily reversible), encryption protects with keys (secure)
* **ROT Ciphers**: Simple letter-shifting ciphers (ROT1 shifts by 1, ROT13 by 13 positions)
* **XOR Cipher**: Each byte is combined with a key using XOR operation, producing random-looking characters
* **CyberChef**: Powerful tool for encoding/decoding operations with "Recipe" feature to chain multiple operations
* **Magic Operation**: Automatically tries common decoders - useful when cipher type is unknown
* **Layered Obfuscation**: Attackers combine multiple techniques (e.g., gzip → XOR → Base64) requiring reverse-order deobfuscation

## Notes:

* The Magic operation doesn't catch everything - custom XOR keys require manual detection
* Visual pattern recognition is key: Base64 ends in `=`, XOR has random symbols, ROT preserves spaces
* For layered obfuscation, operations must be reversed in opposite order (like peeling an onion)
* Real malware often uses far more complex obfuscation than these examples
* PowerShell scripts are commonly obfuscated for malware delivery

* [Advent of Cyber 2025 Day 18](https://tryhackme.com/room/adventofcyber2025)

***
