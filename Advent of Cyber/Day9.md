# A Cracking Christmas

Learn how password-based encryption protects files and how attackers exploit weak passwords using dictionary and brute-force attacks to crack encrypted PDFs and ZIP archives.

## Solution:

**Step 1: Understanding the Challenge**

- Sir Carrotbane discovered encrypted PDF and ZIP files labeled "North Pole Asset List"
- The files contain fragments of Santa's master gift registry
- Goal: Crack the encryption by exploiting weak passwords

**Step 2: Navigating to the Files**

- Connected to the target machine via SSH or split view
- Navigated to the Desktop directory where the encrypted files were located:

```
cd Desktop
```

- Verified the file types to determine the appropriate cracking tools:

```
file flag.pdf
file flag.zip
```

**Step 3: Cracking the Encrypted PDF**

- Used `pdf2john` to extract the password hash from the PDF:

```
pdf2john flag.pdf > pdf_hash.txt
```

- Used John the Ripper with the rockyou.txt wordlist to crack the password:

```
john --wordlist=/usr/share/wordlists/rockyou.txt pdf_hash.txt
```

Output:
```
Using default input encoding: UTF-8
Loaded 1 password hash (PDF)
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort
naughtylist      (flag.pdf)
1g 0:00:00:45 DONE
Session completed.
```

- Successfully recovered password: **naughtylist**
- Opened the PDF using the cracked password to retrieve the first flag

![PDF opened with password revealing flag](https://via.placeholder.com/800x400?text=PDF+Flag+Revealed)

**Step 4: Cracking the Encrypted ZIP**

- Used `zip2john` to extract the password hash from the ZIP file:

```bash
zip2john flag.zip > zip_hash.txt
```

- Used John the Ripper with the rockyou.txt wordlist:

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt zip_hash.txt
```

Output:
```
Using default input encoding: UTF-8
Loaded 1 password hash (ZIP, WinZip [PBKDF2-SHA1 128/128 AVX 4x])
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort
winter4ever      (flag.zip/flag.txt)
1g 0:00:02:12 DONE
Session completed.
```

- Successfully recovered password: **winter4ever**
- Extracted the ZIP file using the cracked password:

```
unzip flag.zip
```

- Read the extracted flag.txt file to retrieve the second flag

![ZIP extracted with flag file contents](https://via.placeholder.com/800x400?text=ZIP+Flag+Revealed)

## Flags:

**PDF Flag:**
```
THM{Cr4ck1ng_PDFs_1s_34$y}
```

**ZIP Flag:**
```
THM{Cr4ck1n6_z1p$_1s_34$yyyy}
```

## Concepts learnt:

* **Password-Based Encryption**: Files like PDFs and ZIPs can be protected using encryption algorithms, but the security depends entirely on password strength. Weak passwords can be cracked, exposing encrypted contents.

* **Dictionary Attacks**: A password cracking technique that uses predefined wordlists (like rockyou.txt) containing common passwords, leaked credentials, and predictable patterns. Fast and effective against weak passwords.

* **Mask Attacks (Brute-Force)**: Systematically tries every possible character combination. Can be optimized using masks (e.g., `?l?l?l?d?d` for 3 lowercase letters + 2 digits) to reduce search space based on known password patterns.

* **John the Ripper**: A powerful password cracking tool that supports multiple hash formats. Uses helper scripts like `pdf2john` and `zip2john` to extract hashes from encrypted files.

* **Hash Extraction**: Before cracking, passwords must be extracted as hashes from encrypted files. Tools like `pdf2john` and `zip2john` convert file encryption into a format that cracking tools can process.

* **Wordlist Selection**: rockyou.txt is a popular wordlist containing over 14 million real-world passwords from data breaches, making it highly effective for dictionary attacks.

## Notes:

* Alternative tools for cracking:
  - **pdfcrack**: Specialized tool for PDF files
  - **fcrackzip**: Dedicated ZIP password cracker
  - **hashcat**: GPU-accelerated cracking (much faster for complex passwords)

* The strength of encryption doesn't matter if the password is weak - short or common passwords can be guessed quickly regardless of encryption algorithm strength.

* Different file formats use different encryption schemes:
  - PDF uses its own encryption standard
  - ZIP files (especially older ones) may use weaker encryption that's easier to crack

* Detection methods for password cracking activities:
  - Process monitoring for tools like john, hashcat, pdfcrack
  - GPU usage spikes (when using GPU acceleration)
  - Access to wordlist files like rockyou.txt
  - Command-line patterns containing `--wordlist`, `-w`, `zip2john`, etc.

* **Defense recommendations**:
  - Use long, complex, random passwords (minimum 16+ characters)
  - Avoid dictionary words, names, dates, or common patterns
  - Implement MFA where possible
  - Use password managers to generate and store strong passwords

* Time taken for cracking depends on:
  - Password complexity and length
  - Wordlist size and quality
  - Hardware resources (CPU/GPU)
  - Encryption algorithm strength

## Resources:

* [TryHackMe - Advent of Cyber 2025 Day 9](https://tryhackme.com/r/room/adventofcyber2025)
* [John the Ripper Documentation](https://www.openwall.com/john/doc/)
