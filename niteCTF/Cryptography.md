# hash_vegas
I think the casino is broken. I bought a ticket, the system said I won, and then my bank account instantly hit zero.

## Solution:

The goal of this challenge is to increase our balance to above 1 billion dollars to obtain the flag. The casino offers three games: slot machine, roulette, and lottery tickets, but the regular winnings won't be enough to reach our target.

### Step 1: Understanding the Vulnerability

The lottery system has a critical vulnerability in how it generates ticket vouchers:

```
hash_func = self.hash_funcs[hash_idx]
ticket_data = f"{username}|{amount}"
ticket_hash = hash_func((self.secret + ticket_data).encode()).digest()[:20]
```

The hash function is randomly selected from:
```
self.hash_funcs = [hashlib.sha256]*1024+[hashlib.sha3_224]*1023+[hashlib.sha1]
```

This is vulnerable to a **hash length extension attack**, but only SHA-1 supports this attack (probability: 1/2048). The catch is that winning the lottery sets your balance to zero, so we can only do this once.

### Step 2: Recovering the Mersenne Twister State

To predict when SHA-1 will be used, we need to recover Python's random number generator (Mersenne Twister) state.

**From Slot Machine (56 spins):**
- Each spin generates 2×32-bit numbers using `random.choices()`
- The slot machine effectively behaves like `getrandbits(32)` 
- Total: 56 spins × 2 = 112 outputs = 3,584 bits

```
def emoji2val(e):
    val = ''
    for i in e:
        val = bin(emojis.index(i))[2:].zfill(4) + val
    return int(val,2)

def get_slotval(no=False):
    r.recvuntil(b'Enter your choice: ')
    r.sendline(b'1')
    data = r.recvuntil(b'Updated balance: $').decode('utf-8')
    wheel = ''
    for c in data:
        if c in emojis:
            wheel+=c
    if not no:
        rc.submit(emoji2val(wheel[:8]))
        rc.submit(emoji2val(wheel[8:]))
```

**From Roulette (64 plays):**
- Each play generates a 256-bit number using `random.randrange(0, 2^256-1)`
- Since the upper bound is close to 2^256, only one `getrandbits(256)` call is needed
- Total: 64 plays × 256 bits = 16,384 bits

```
def submit_256(val):
    for i in range(8):
        shift = i * 32
        part = (val >> shift) & 0xFFFFFFFF
        rc.submit(part)

def get_rouletteval():
    r.recvuntil(b'Enter your choice: ')
    r.sendline(b'2')
    r.sendline(b'1')
    r.sendline(b'R')
    r.recvuntil(b'the number is ')
    num = int(r.recvline().strip().decode())
    r.recvuntil(b'Updated balance: $')
    submit_256(num)
```

Combined, we have 3,584 + 16,384 = 19,968 bits, which is exactly enough to recover the 624×32-bit Mersenne Twister state!

### Step 3: Predicting SHA-1 Usage

Once we recover the MT state, we can predict future random numbers. We buy $0 lottery tickets (to advance the RNG state without losing money) until we predict SHA-1 will be used:

```
def get_lottery():
    shuffle(hash_funcs)
    sha1_idx = hash_funcs.index(hashlib.sha1)
    found = False
    while not found:
        ticket_id = random.randint(1, 11)
        hash_idx = random.randint(0, len(hash_funcs) - 1)
        if ticket_id > 5 and hash_idx == sha1_idx:
            found = True
            break
        r.recvuntil(b'Enter your choice: ')
        r.sendline(b'3')
        r.sendline(b'0')  # Buy $0 ticket
    r.sendline(b'3')
    r.sendline(b'1')  # Buy winning ticket
```

### Step 4: Hash Length Extension Attack

When we get a SHA-1 voucher, we perform a length extension attack to change the amount from the original value to 1,000,000,000:

```
def forge_lottery(username,amount,vcode):
    original_data = (username+'|'+str(amount)).encode()
    append_data = b"|1000000000"
    magic = HashTools.new("sha1")
    new_data, new_code = magic.extension(
        secret_length=32,
        original_data=original_data,
        append_data=append_data,
        signature=vcode
    )
    return new_data,new_code
```

### Step 5: Complete Exploit

```
from random import *
from randcrack import RandCrack
from pwn import *
import hashlib
import HashTools

HOST = ...
PORT = ...
emojis = ['🍒', '🍋', '🍊', '🍇','🍉', '🍓', '🍍', '🍎',
          '🍏', '🍐', '🍑', '🍈','🍌', '🥭', '🥝', '🥥']
hash_funcs = [hashlib.sha256]*1024+[hashlib.sha3_224]*1023+[hashlib.sha1]
user = 'user'

r = remote(HOST,PORT)
rc = RandCrack()

r.recvuntil(b'username: ')
r.sendline(user.encode())

# Collect slot machine outputs
for _ in range(56):
    get_slotval()

# Collect roulette outputs
for _ in range(64):
    get_rouletteval()

# Recover MT state
setstate((3, (*[int(''.join(map(str, rc.mt[i])), 2) for i in range(len(rc.mt))], 0), None))

# Get SHA-1 lottery ticket
amount,vdata,vcode = get_lottery()

# Forge ticket for $1 billion
ndata,ncode = forge_lottery(user,amount,vcode)

# Redeem forged ticket
r.sendline(b'4')
r.sendline(ncode.encode())
r.sendline(ndata.hex().encode())

# Get flag
r.sendline(b'6')
r.recvuntil(b"Here's your flag: ")
print(r.recvline())
```

## Flag:

```
nite{9ty%_0f_g4mbler5_qu17_b3f0re_th3y_mak3_1t_big}
```

## Concepts learnt:

- **Mersenne Twister State Recovery**: Python's `random` module uses the MT19937 algorithm, which can be fully predicted after observing 624 consecutive 32-bit outputs (19,968 bits). The `randcrack` library automates this recovery.

- **Hash Length Extension Attack**: For hash functions using Merkle-Damgård construction (like SHA-1, SHA-256, but NOT SHA-3), if you know `H(secret || data)` and the length of `secret`, you can compute `H(secret || data || padding || extra_data)` without knowing the secret. This works because these hash functions process data in blocks and their internal state after processing becomes the hash output.

- **Python Random Internals**: Understanding how `random.choices()`, `random.randrange()`, and `getrandbits()` work under the hood is crucial. The `_randbelow()` method repeatedly calls `getrandbits(k)` until getting a value less than n, but when n is close to 2^k, this almost always succeeds on the first try.

- **Strategic Information Gathering**: We used different casino games to extract different amounts of entropy from the RNG - slot machines for 64-bit chunks and roulette for 256-bit chunks.

## Notes:

- The probability of SHA-1 being selected is only 1/2048, but by recovering the RNG state we can predict exactly when it will occur without wasting money on failed attempts.

- Winning the lottery sets your balance to zero, so the length extension attack must be successful on the first try - there's no room for error.

- The challenge demonstrates why cryptographic operations should never rely on predictable PRNGs like Mersenne Twister. Use cryptographically secure random number generators (like `secrets` module in Python) for security-sensitive operations.

- An alternative (but much harder) approach would be to try to exploit timing or other side channels, but the RNG prediction approach is much more reliable.

## Resources:

- (https://github.com/tna0y/Python-random-module-cracker)
- (https://blog.skullsecurity.org/2012/everything-you-need-to-know-about-hash-length-extension-attacks)
- (https://github.com/bwall/HashPump)
- (https://en.wikipedia.org/wiki/Mersenne_Twister)
- (https://github.com/python/cpython/blob/main/Lib/random.py)
  
***

# stronk_rabin

rabin weak. I make it stronk. oh no. it broke.

## Solution:

This challenge involves breaking a "strengthened" Rabin cryptosystem. The vulnerability lies in how the decryption oracle leaks information about the modulus and plaintext through multiple possible square roots.

### Step 1: Understanding the Rabin Cryptosystem

The Rabin cryptosystem encrypts messages as:
```
c ≡ m² (mod n)
```

Where `n = p × q` for primes `p` and `q`. Decryption has four possible square roots due to the Chinese Remainder Theorem (CRT):
- `m`
- `-m (mod n)`
- `m'` (another valid root)
- `-m' (mod n)`

The challenge implements a "stronk" (strong) version that randomizes which root is returned by the decryption oracle.

### Step 2: Querying the Decryption Oracle

We repeatedly query the `DEC` function with the same ciphertext to collect all possible plaintext candidates:

```
from pwn import *
from Crypto.Util.number import *
import json

r = remote('localhost', 9999)
r.recvline()
c = json.loads(r.recvline().decode().strip())["C"]

possibles = set()
flags = set()

while len(flags) != 153:
    tosend = json.dumps({'func' : 'DEC', 'args' : [c]})
    r.sendline(tosend.encode())
    uh = json.loads(r.recvline().decode())['retn']
    flags.add(int(uh))
    print(len(flags))
```

This collects all distinct values the oracle can return. The number 153 suggests there are specific combinations being returned based on the CRT construction.

### Step 3: Recovering the Modulus N

The key insight is that among all the returned values, the **largest** and **smallest** values form a complementary pair:
- Maximum value: `x`
- Minimum value: `-x (mod n)` which equals `n - x`

Therefore:
```
n = max_value + min_value
```

```python
ok = sorted(list(flags))
N = ok[-1] + ok[1]  # largest + smallest (note: ok[0] might be 0 or special case)
print(f"{N = }")
```

### Step 4: Understanding the CRT Sum Property

Due to how the Chinese Remainder Theorem works with Rabin decryption, the oracle returns various combinations. One crucial property is that some combination in the returned set corresponds to `2m (mod n)` where `m` is the original plaintext.

This happens because the CRT can produce sums of roots that effectively double the plaintext value modulo n.

### Step 5: Recovering the Original Plaintext

To recover the original message `m` from the potential `2m` values, we:
1. Multiply each returned value by `2⁻¹ (mod n)`
2. Convert to bytes
3. Search for the flag format

```python
mults = [long_to_bytes((inverse(2, N) * i) % N) for i in ok]

for i in mults:
    if b'nite' in i:
        print(i)
```

### Exploit Script

```
from pwn import *
from Crypto.Util.number import *
import json

r = remote('localhost', 9999)
r.recvline()
c = json.loads(r.recvline().decode().strip())["C"]

possibles = set()
flags = set()

# Collect all possible decryption outputs
while len(flags) != 153:
    tosend = json.dumps({'func' : 'DEC', 'args' : [c]})
    r.sendline(tosend.encode())
    uh = json.loads(r.recvline().decode())['retn']
    flags.add(int(uh))
    print(len(flags))

# Sort collected values
ok = sorted(list(flags))

# Recover modulus from largest and smallest values
N = ok[-1] + ok[1]
print(f"{N = }")

# Try multiplying each value by 2^-1 mod N
mults = [long_to_bytes((inverse(2, N) * i) % N) for i in ok]

# Find the flag
for i in mults:
    if b'nite' in i:
        print(i)
```

## Flag:

```
nite{rabin_stronk?_no_r4bin_brok3n}
```


## Concepts learnt:

- **Rabin Cryptosystem Weaknesses**: Unlike RSA where there's one unique decryption, Rabin decryption yields four possible plaintexts due to the four square roots modulo n. This ambiguity is both a feature (for certain protocols) and a weakness when combined with an oracle.

- **Chinese Remainder Theorem (CRT) in Cryptography**: When computing square roots modulo n = p×q, we first compute square roots modulo p and modulo q separately, then use CRT to combine them. This gives us 2×2 = 4 possible combinations.

- **Modulus Recovery from Complementary Pairs**: If we can observe values x and -x (mod n), we can recover n by adding them: `x + (n-x) = n`. This works because modular arithmetic wraps around at n.

- **Oracle Attacks**: Even with randomization, repeatedly querying a decryption oracle can leak enough information to break the system. This is why CCA (Chosen Ciphertext Attack) security is important.

- **Multiplicative Property of Modular Arithmetic**: If we have `2m (mod n)`, we can recover `m` by multiplying by the modular inverse of 2: `2⁻¹ × 2m ≡ m (mod n)`.

## Notes:

- The number 153 is interesting - it might represent all unique CRT combinations or be related to how the "stronk" implementation handles the four roots plus their various sums/differences.

- The challenge title plays on "weak" → "stronk" (internet slang for "strong"), but the strengthening actually makes it exploitable by providing more information through the oracle.

- An alternative approach could involve analyzing the mathematical relationships between all 153 values to understand the exact CRT combination structure, but the practical approach of trying `2⁻¹` multiplication is more efficient.

- Real-world Rabin implementations must be careful about:
  - Not providing decryption oracles
  - Adding proper padding (like OAEP) to prevent mathematical attacks
  - Including redundancy to identify the correct root among the four possibilities

- The use of `ok[1]` instead of `ok[0]` for the minimum suggests that index 0 might contain a special value (possibly 0 or 1), which is worth investigating if the exploit fails.

## Resources:

- (https://en.wikipedia.org/wiki/Rabin_cryptosystem)
- (https://crypto.stanford.edu/pbc/notes/numbertheory/crt.html)
- (https://en.wikipedia.org/wiki/Modular_multiplicative_inverse)
- (https://www.cs.umd.edu/~jkatz/gradcrypto2/NOTES/lecture5.pdf)
- (https://pycryptodome.readthedocs.io/)

***
# symmetric_starter

just learnt how to encrypt my messages using a symmetric cipher. i hope no one can read it now.

## Solution:

This challenge implements a custom stream cipher that leaks the Most Significant Bit (MSB) of its internal nonce state. By collecting enough leaked bits, we can reconstruct the nonce evolution and recover the original AES key.

### Step 1: Analyzing the Encryption Scheme

The cipher works as follows:
1. A nonce is initialized from the AES key
2. For each round:
   - The MSB of the nonce is leaked
   - The leaked bits (as a cumulative number) are added to the nonce
   - The nonce is rotated left by N positions
3. AES-ECB encrypts the nonce to produce keystream blocks
4. Plaintext blocks are XORed with keystream blocks

The critical vulnerability is that **128 MSB leaks provide enough information to reconstruct the initial key**.

### Step 2: Setting Up the Constraint Solver

We'll use Z3 to model the nonce evolution symbolically. The key is represented as 16 unknown bytes that we'll solve for:

```
from z3 import *

# Read the challenge output
with open("out.txt") as f:
    ciphertext_hex = f.readline().strip()
    leaked_bits = f.readline().strip()

ct = bytes.fromhex(ciphertext_hex)
leak_array = [int(bit) for bit in f"{int(leaked_bits, 16):0128b}"]

# Create symbolic key bytes
symbolic_key = [BitVec(f'key_{idx}', 8) for idx in range(16)]
initial_nonce = Concat(*symbolic_key)
```

### Step 3: Modeling the Nonce Evolution

We simulate the nonce transformation for 128 rounds, adding constraints that match the leaked bits:

```
ROTATION_AMOUNT = 3
solver = Solver()

current_nonce = initial_nonce
accumulated_leak = 0

for round_num in range(128):
    expected_bit = leak_array[round_num]
    
    # The MSB must match the leaked bit
    solver.add(Extract(127, 127, current_nonce) == expected_bit)
    
    # Update accumulated leak value
    accumulated_leak = (accumulated_leak << 1) | expected_bit
    
    # Simulate nonce update: nonce = (nonce + leak) mod 2^128
    current_nonce = current_nonce + accumulated_leak
    
    # Rotate left by N bits
    current_nonce = RotateLeft(current_nonce, ROTATION_AMOUNT)
```

### Step 4: Extracting the Key

Once Z3 finds a satisfying assignment, we extract the key bytes:

```
if solver.check() == sat:
    model = solver.model()
    
    # Reconstruct the key from the model
    key_recovered = bytearray()
    for kb in symbolic_key:
        key_recovered.append(model[kb].as_long())
    
    print(f"[+] Recovered key (hex): {key_recovered.hex()}")
    print(f"[+] Recovered key (bytes): {key_recovered}")
else:
    print("[-] No solution found!")
    exit(1)
```

**Output:**
```
[+] Recovered key (hex): 0148a672548041e25c87ea397d3f5a70
[+] Recovered key (bytes): bytearray(b'\x01H\xa6rT\x80A\xe2\\\x87\xea9}?Zp')
```

### Step 5: Implementing Decryption

Now we reverse the encryption process using the recovered key:

```
from Crypto.Cipher import AES
from Crypto.Util.number import bytes_to_long
from pwn import xor

def rotate_left_128bit(value, bits):
    """Rotate a 128-bit number left by 'bits' positions"""
    mask = (1 << 128) - 1
    return ((value << bits) | (value >> (128 - bits))) & mask

def generate_keystream(aes_key):
    """Generate keystream blocks using the custom algorithm"""
    cipher = AES.new(aes_key, AES.MODE_ECB)
    nonce_value = bytes_to_long(aes_key)
    leak_binary = ""
    
    while True:
        # Leak MSB
        msb = nonce_value >> 127
        leak_binary += str(msb)
        
        # Update nonce
        leak_int = int(leak_binary, 2)
        nonce_value = (nonce_value + leak_int) & ((1 << 128) - 1)
        
        # Yield encrypted nonce as keystream
        yield cipher.encrypt(nonce_value.to_bytes(16, 'big'))
        
        # Rotate nonce
        nonce_value = rotate_left_128bit(nonce_value, ROTATION_AMOUNT)

def decrypt_message(ciphertext, key):
    """Decrypt the ciphertext using the recovered key"""
    if len(ciphertext) % 16 != 0:
        raise ValueError("Ciphertext length must be multiple of 16")
    
    # Split into 16-byte blocks
    cipher_blocks = [ciphertext[i:i+16] for i in range(0, len(ciphertext), 16)]
    
    # Generate keystream and decrypt
    keystream_gen = generate_keystream(key)
    plaintext = b''
    
    for block in cipher_blocks:
        keystream_block = next(keystream_gen)
        plaintext += xor(block, keystream_block)
    
    return plaintext
```

### Step 6: Recovering the Flag

Decrypt the ciphertext and search for the flag format:

```
import re

plaintext = decrypt_message(ct, bytes(key_recovered))

# Search for flag pattern
flag_match = re.search(rb'nite\{[^}]+\}', plaintext)

if flag_match:
    flag = flag_match.group().decode()
    print(f"[+] Flag found: {flag}")
else:
    print("[-] Flag not found in plaintext")
    print(f"[*] Decrypted text: {plaintext}")
```

**Output:**
```
Flag found: nite{wh00ps_l34k3d_2_mUch}
```

### Complete Solve Script

```
from Crypto.Cipher import AES
from Crypto.Util.number import bytes_to_long
from pwn import xor
from z3 import *
import re

# Configuration
ROTATION_AMOUNT = 3
TOTAL_ROUNDS = 128

# Parse input
with open("out.txt") as f:
    ciphertext_hex = f.readline().strip()
    leaked_bits = f.readline().strip()

ct = bytes.fromhex(ciphertext_hex)
leak_array = [int(bit) for bit in f"{int(leaked_bits, 16):0{TOTAL_ROUNDS}b}"]

print(f"[*] Ciphertext length: {len(ct)} bytes")
print(f"[*] Number of leaked bits: {len(leak_array)}")

# Set up Z3 solver
symbolic_key = [BitVec(f'key_{idx}', 8) for idx in range(16)]
initial_nonce = Concat(*symbolic_key)

solver = Solver()
current_nonce = initial_nonce
accumulated_leak = 0

print("[*] Building constraints...")
for round_num in range(TOTAL_ROUNDS):
    expected_bit = leak_array[round_num]
    solver.add(Extract(127, 127, current_nonce) == expected_bit)
    accumulated_leak = (accumulated_leak << 1) | expected_bit
    current_nonce = current_nonce + accumulated_leak
    current_nonce = RotateLeft(current_nonce, ROTATION_AMOUNT)

# Solve for the key
print("[*] Solving constraints...")
if solver.check() == sat:
    model = solver.model()
    key_recovered = bytearray()
    for kb in symbolic_key:
        key_recovered.append(model[kb].as_long())
    
    print(f"[+] Recovered key (hex): {key_recovered.hex()}")
else:
    print("[-] No solution found!")
    exit(1)

# Decryption functions
def rotate_left_128bit(value, bits):
    mask = (1 << 128) - 1
    return ((value << bits) | (value >> (128 - bits))) & mask

def generate_keystream(aes_key):
    cipher = AES.new(aes_key, AES.MODE_ECB)
    nonce_value = bytes_to_long(aes_key)
    leak_binary = ""
    
    while True:
        msb = nonce_value >> 127
        leak_binary += str(msb)
        leak_int = int(leak_binary, 2)
        nonce_value = (nonce_value + leak_int) & ((1 << 128) - 1)
        yield cipher.encrypt(nonce_value.to_bytes(16, 'big'))
        nonce_value = rotate_left_128bit(nonce_value, ROTATION_AMOUNT)

def decrypt_message(ciphertext, key):
    cipher_blocks = [ciphertext[i:i+16] for i in range(0, len(ciphertext), 16)]
    keystream_gen = generate_keystream(key)
    plaintext = b''
    for block in cipher_blocks:
        plaintext += xor(block, next(keystream_gen))
    return plaintext

# Decrypt and find flag
print("[*] Decrypting message...")
plaintext = decrypt_message(ct, bytes(key_recovered))

flag_match = re.search(rb'nite\{[^}]+\}', plaintext)
if flag_match:
    print(f"[+] Flag found: {flag_match.group().decode()}")
else:
    print("[-] Flag not found")
```

## Flag:

```
nite{wh00ps_l34k3d_2_mUch}
```

## Concepts learnt:

- **Information Leakage in Stream Ciphers**: Even seemingly small leaks (like single bits) can compromise the entire system if enough leaks are collected. The MSB leak in 128 rounds provided sufficient information to reconstruct the full 128-bit state.

- **Symbolic Execution with Z3**: Z3 is a powerful SMT (Satisfiability Modulo Theories) solver that can work with symbolic variables and constraints. We modeled the cipher's state transitions symbolically and let Z3 find values that satisfy all constraints.

- **Bit Vector Operations in Z3**: Z3's BitVec type allows us to work with fixed-width integers and perform operations like `Extract` (getting specific bits), `Concat` (joining bit vectors), and `RotateLeft` (bitwise rotation).

- **Custom Stream Cipher Vulnerabilities**: This challenge demonstrates why designing custom cryptography is dangerous. The nonce update mechanism (`nonce = (nonce + accumulated_leak) mod 2^128`) combined with MSB leaks creates a mathematical relationship that can be reversed.

- **State Space Reduction**: Initially, there are 2^128 possible keys. Each leaked bit cuts this space in half, so 128 bits theoretically reduce it to exactly 1 (or a few) solutions. The rotation adds complexity but doesn't prevent the attack.

## Notes:

- The rotation amount (3 bits) affects how quickly the state mixes but doesn't prevent the attack since we're tracking the complete state evolution through Z3.

- An interesting variant would be to leak fewer bits (say, 64 or 96) and see if partial key recovery is still possible, potentially requiring brute-forcing remaining bits.

- The challenge could have been made harder by:
  - Using a cryptographic hash function instead of simple addition
  - Leaking bits from random positions instead of always the MSB
  - Adding more non-linear operations in the state update
  
- Alternative solving approaches:
  - Linear algebra over GF(2) if the operations were purely linear
  - Meet-in-the-middle attacks if fewer bits were leaked
  - SAT solvers (like CryptoMiniSat) instead of Z3

## Resources:

-(https://ericpony.github.io/z3py-tutorial/guide-examples.htm)
-(https://z3prover.github.io/api/html/namespacez3py.html)
-(https://en.wikipedia.org/wiki/Stream_cipher_attacks)
-(https://www.rambus.com/blogs/side-channel-attacks/)
-(https://www.cs.toronto.edu/~alexm/research/crypto-smt.pdf)
