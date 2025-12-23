# **ICS - Modbus – Claus for Concern**

# **Task 1 – Introduction**

## **The Story**

The snow falls heavily over Wareville as chaos erupts at TBFC headquarters. What should be the busiest shipping day of the season has turned into a disaster.

**"Another chocolate egg?!"** shouts a frustrated warehouse worker, holding up yet another Easter-themed package. **"We're supposed to be shipping Christmas presents!"**

The delivery drones buzz overhead, their mechanical hums sounding almost... mocking. Each one returns from its route empty, having successfully delivered its cargo. But the cargo is all wrong.

You're called into the command centre, where screens flicker with delivery statistics. Everything looks normal on the surface—1,000 presents in stock, 98% success rate, and all systems are operational. But the phones won't stop ringing with confused citizens asking why they're receiving chocolate eggs instead of the toys and gifts they ordered.

The logistics manager pulls up a delivery manifest. "Look at this", she says, pointing at the screen. "The system indicates that we delivered a teddy bear to the Miller family, but they received a chocolate bunny instead. It's the same weight, exact dimensions, but completely different items."

Then, on one of the monitoring screens, a message flashes for just a second before disappearing:

```
EGGSPLOIT v6.66 - Property of HopSec Island
"Why should Christmas have all the fun?" - King Malhare
```

Someone has compromised the drone fleet's control systems. The attack is sophisticated, falsifying sensor data, manipulating inventory selection, and erasing all traces. This isn't just a prank—it's a calculated assault on Christmas itself.

**Your mission:** Investigate the TBFC Drone Delivery System, uncover how King Malhare's Eggsploit team has compromised it, and restore Christmas deliveries before SOC-mas is ruined.

**Warning:** King Malhare doesn't leave systems undefended. Traps are waiting for the careless investigator. One wrong move and you might make things much worse.

---

## **A Mysterious Discovery**

As you walk through the warehouse control room, something catches your eye—a crumpled piece of paper on the floor near the PLC terminal. It looks like someone dropped it in a hurry.

You pick it up and unfold it. The handwriting is hurried, almost frantic:

```
TBFC DRONE CONTROL - REGISTER MAP
(For maintenance use only)

HOLDING REGISTERS:
HR0: Package Type Selection
     0 = Christmas Gifts
     1 = Chocolate Eggs
     2 = Easter Baskets

HR1: Delivery Zone (1-9 normal, 10 = ocean dump!)

HR4: System Signature/Version
     Default: 100
     Current: ??? (check this!)

COILS (Boolean Flags):
C10: Inventory Verification
     True = System checks actual stock
     False = Blind operation

C11: Protection/Override
     True = Changes locked/monitored
     False = Normal operation

C12: Emergency Dump Protocol
     True = DUMP ALL INVENTORY
     False = Normal

C13: Audit Logging
     True = All changes logged
     False = No logging

C14: Christmas Restored Flag
     (Auto-set when system correct)

C15: Self-Destruct Status
     (Auto-armed on breach)

CRITICAL: Never change HR0 while C11=True!
Will trigger countdown!

- Maintenance Tech, Dec 19
```

You stare at the note, confusion washing over you. "Register map? Coils? What is all this?"

The terminology is foreign—HR0, C11, "Modbus" scribbled in the margin. But something about it feels important, like a key you don't yet know how to use.

You pocket the note carefully. "I'll figure out what this means later", you think. For now, you need to understand the systems you're dealing with.

Little do you know, this crumpled note will be exactly what saves Christmas...

---

## **Learning Objectives**

- Understand how **SCADA** (Supervisory Control and Data Acquisition) systems monitor industrial processes
- Learn what **PLCs** (Programmable Logic Controllers) do in automation
- Explore how the **Modbus protocol** enables communication between industrial devices
- Identify **compromised system configurations** in industrial systems
- Learn techniques for **safely remediating** compromised control systems
- Understand **protection mechanisms and trap logic** in ICS environments

---

# **Task 2 – SCADA Systems**

## **What is SCADA?**

**SCADA systems** are the "command centres" of industrial operations. They act as the bridge between human operators and the machines doing the work. Think of SCADA as the nervous system of a factory—it senses what's happening, processes that information, and sends commands to make things happen.

TBFC uses a SCADA system to oversee its entire drone delivery operation. Without it, operators would have no way to monitor hundreds of drones, manage inventory, or ensure packages reach the right destinations. It's the invisible orchestrator of Christmas logistics.

---

## **Components of a SCADA System**

A SCADA system typically consists of four key components:

### **1. Sensors & Actuators**

These are the **eyes and hands** of the system.

- **Sensors** measure real-world conditions: temperature, pressure, position, weight
- **Actuators** perform physical actions: motors turn, valves open, robotic arms move

**Example:** In TBFC's warehouse, sensors detect when a package is placed on the conveyor belt, and actuators control the robotic arms that load drones.

### **2. PLCs (Programmable Logic Controllers)**

These are the **brains** that execute automation logic.

- Read sensor data
- Make decisions based on programmed rules
- Send commands to actuators

**Example:** A PLC might decide: "If the package weight matches a chocolate egg AND the destination is Zone 5, load it onto Drone 7."

### **3. Monitoring Systems**

Visual interfaces where operators observe physical processes:

- CCTV cameras
- Dashboards
- Alarm panels

**Example:** TBFC's warehouse has security cameras on **port 80** that show real-time footage of the packaging floor.

### **4. Historians**

Databases that store operational data for later analysis:

- Every package loaded
- Every drone launched
- Every system change gets recorded

**Purpose:** Identify patterns, troubleshoot problems, and reconstruct attacker actions during incident response.

---

## **SCADA in the Drone Delivery System**

TBFC's compromised SCADA system manages several critical functions:

| Function                   | Description                                                                                              |
| -------------------------- | -------------------------------------------------------------------------------------------------------- |
| **Package Type Selection** | Decides whether to load Christmas gifts, chocolate eggs, or Easter baskets (controlled by numeric value) |
| **Delivery Zone Routing**  | Each package must reach the correct neighbourhood (Zones 1-9: districts; Zone 10: ocean disposal)        |
| **Visual Monitoring**      | CCTV camera feed on port 80 provides real-time observation of warehouse floor                            |
| **Inventory Verification** | Checks whether requested item exists in stock before loading                                             |
| **System Protection**      | Security features to prevent unauthorized changes (weaponized by King Malhare)                           |
| **Audit Logging**          | Records every configuration change, login, and modification                                              |

---

## **Why SCADA Systems Are Targeted**

Industrial control systems like SCADA have become increasingly attractive targets for cybercriminals and nation-state actors:

### **Vulnerabilities**

1. **Legacy Software** — Many SCADA systems were installed decades ago and never updated
2. **Default Credentials** — Commonly left unchanged ("if it works, don't touch it" mentality)
3. **Designed for Reliability, Not Security** — Built before cybersecurity was a concern
4. **Control Physical Processes** — Can cause real-world consequences (blackouts, contamination, sabotage)
5. **Connected to Corporate Networks** — Most are not truly "air-gapped"
6. **Protocols Lack Authentication** — Modbus and similar protocols were designed for trusted environments

### **Real-World Impact**

- In early **2024**, the first ICS/OT malware, **FrostyGoop**, was discovered
- Can directly interface with industrial control systems via **Modbus TCP protocol**
- Enables arbitrary reads and writes to device registers over **TCP port 502**

**King Malhare** has weaponized these same tactics to sabotage Christmas deliveries by directly manipulating the control system through the Modbus protocol.

---

## **Questions**

### **What port is commonly used by Modbus TCP?**

<details>
  <summary>Click to reveal the answer</summary>

**502**

</details>

---

# **Task 3 – PLC & Modbus Protocol**

## **What is a PLC?**

A **PLC** (Programmable Logic Controller) is an industrial computer designed to control machinery and processes in real-world environments. Unlike your laptop or smartphone, PLCs are purpose-built machines engineered for extreme reliability and harsh conditions.

### **PLCs are designed to:**

| Feature                                       | Description                                                                                  |
| --------------------------------------------- | -------------------------------------------------------------------------------------------- |
| **Survive Harsh Environments**                | Operate in extreme temperatures, vibration, dust, moisture, and electromagnetic interference |
| **Run Continuously Without Failure**          | Operate 24/7 for years (sometimes decades) without rebooting                                 |
| **Execute Control Logic in Real-Time**        | Respond to sensor inputs within milliseconds                                                 |
| **Interface Directly with Physical Hardware** | Connect directly to sensors and actuators, speaking the electrical language of machinery     |

**Example:** When a package reaches the end of a conveyor belt, the PLC must instantly activate the robotic arm to catch it. These timing requirements are critical for safety and efficiency.

---

## **What is Modbus?**

**Modbus** is the communication protocol that industrial devices use to talk to each other.

- Created in **1979** by Modicon (now Schneider Electric)
- One of the oldest and most widely deployed industrial protocols in the world
- Succeeded because it's **simple**, **reliable**, and works with almost any device

### **How Modbus Works**

Think of Modbus as a basic request-response conversation:

```
Client (your computer): "PLC, what's the current value of register 0?"
Server (the PLC): "Register 0 currently holds the value 1."
```

### **The Security Problem**

This simplicity makes Modbus easy to implement and debug, but it also means **security was never a consideration**:

- ❌ No authentication
- ❌ No encryption
- ❌ No authorization checking

**Result:** Anyone who can reach the Modbus port can read or write any value. It's the equivalent of leaving your house unlocked with a sign saying "Come in, everything's accessible!"

---

## **Modbus Data Types**

Modbus organizes data into four distinct types:

| Type                  | Purpose                  | Values  | Example Use Cases                                 |
| --------------------- | ------------------------ | ------- | ------------------------------------------------- |
| **Coils**             | Digital outputs (on/off) | 0 or 1  | Motor running? Valve open? Alarm active?          |
| **Discrete Inputs**   | Digital inputs (on/off)  | 0 or 1  | Button pressed? Door closed? Sensor triggered?    |
| **Holding Registers** | Analog outputs (numbers) | 0-65535 | Temperature setpoint, motor speed, zone selection |
| **Input Registers**   | Analog inputs (numbers)  | 0-65535 | Current temperature, pressure reading, flow rate  |

### **Important Distinction**

- **Writable:** Coils and Holding Registers (you can change their values to control the system)
- **Read-only:** Discrete Inputs and Input Registers (they reflect sensor measurements)

---

## **TBFC's Drone Control System**

### **Holding Registers (Configuration Values)**

| Register | Purpose                | Values                                        |
| -------- | ---------------------- | --------------------------------------------- |
| **HR0**  | Package type selection | 0=Gifts, 1=Eggs, 2=Baskets                    |
| **HR1**  | Delivery zone          | 1-9 for normal zones, 10 for ocean dump       |
| **HR4**  | System signature       | Version identifier or attacker's calling card |

### **Coils (System Behavior Flags)**

| Coil    | Purpose                      | Values                               |
| ------- | ---------------------------- | ------------------------------------ |
| **C10** | Inventory verification       | True=Enabled, False=Disabled         |
| **C11** | Protection mechanism         | True=Enabled/Monitored, False=Normal |
| **C12** | Emergency dump protocol      | True=Active, False=Inactive          |
| **C13** | Audit logging                | True=Enabled, False=Disabled         |
| **C14** | Christmas restoration status | Auto-set when system is correct      |
| **C15** | Self-destruct mechanism      | Auto-armed/disarmed                  |

**Note:** Remember that crumpled note you found earlier? Now it makes complete sense. The maintenance technician was documenting these exact Modbus addresses and their meanings!

---

## **Modbus Addressing**

Each data point in Modbus has a **unique address**—think of it like a house number on a street.

**Critical detail:** Modbus addresses start at **0**, not 1. This zero-indexing catches many beginners off guard.

### **Examples from TBFC System**

```
Holding Register 0 (HR0) = Package type selector
Holding Register 1 (HR1) = Delivery zone
Holding Register 4 (HR4) = System signature
Coil 10 (C10) = Inventory verification flag
Coil 11 (C11) = Protection mechanism flag
Coil 15 (C15) = Self-destruct status
```

---

## **Modbus TCP vs Serial Modbus**

### **Original: Serial Modbus**

- Operated over serial connections using RS-232 or RS-485 cables
- Devices were physically connected in a network
- Physical isolation provided some security (needed physical access)

### **Modern: Modbus TCP**

- Encapsulates Modbus protocol inside standard TCP/IP network packets
- Modbus TCP servers listen on **port 502** by default
- Benefits: remote monitoring, easier integration, centralized management
- **Risks:** Exposes historically isolated systems to network-based attacks

**King Malhare's Attack:** The TBFC drone control system's Modbus TCP port (502) was accessible over the network without any authentication required. He didn't need to break into the facility or tamper with wiring. He simply connected to port 502 and started issuing commands as if he were authorized.

---

## **The Security Problem**

Modbus has **no built-in security mechanisms:**

| Missing Security Feature  | Impact                                                            |
| ------------------------- | ----------------------------------------------------------------- |
| **No Authentication**     | Protocol doesn't verify who's making requests                     |
| **No Encryption**         | All communication happens in plaintext                            |
| **No Authorization**      | No concept of permissions—if you can connect, you can do anything |
| **No Integrity Checking** | Beyond basic checksums, no cryptographic verification             |

**Modern solutions exist** (VPNs, firewalls, Modbus security gateways), but they're add-ons, not part of the protocol itself. Many industrial facilities haven't implemented these protections due to:

- Cost concerns
- Compatibility issues with legacy equipment
- Lack of awareness

---

## **Connecting the Dots**

Now you understand why the OpenPLC web interface showed nothing useful. **King Malhare bypassed it entirely.**

He connected directly to the Modbus TCP port and manipulated the registers and coils that control system behavior.

**That note you found?** The maintenance technician must have discovered what was happening and started documenting the compromised values. The warning at the bottom—"Never change HR0 while C11=True!"—suggests they figured out the trap mechanism before getting interrupted.

In the next task, we'll use **Python** and the **pymodbus library** to investigate the system exactly the way King Malhare attacked it—by directly reading and writing Modbus values.

---

# **Task 4 – Practical – Hack It Back!**

## **Initial Reconnaissance**

As with any incident response scenario, we begin with reconnaissance. Let's discover what services are running on the target system.

### **Nmap Scan**

From the AttackBox terminal, run an Nmap scan:

```bash
nmap -sV -p 22,80,502 MACHINE_IP
```

**Output:**

```
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.11 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http    Werkzeug httpd 3.1.3 (Python 3.12.3)
502/tcp  open  modbus  Modbus TCP
```

**Note:** We specified ports 22, 80, 502 to speed up the scan. For a comprehensive scan of all ports, use: `nmap -sV -T4 -p- -vv MACHINE_IP` (this will take several minutes).

### **Key Findings**

- **Port 80** — HTTP service (the CCTV camera feed)
- **Port 502** — Modbus TCP (the PLC communication protocol)

This is typical of an industrial control system setup: a web interface for monitoring and Modbus for programmatic control.

---

## **Visual Confirmation: The CCTV Feed**

Before diving into technical details, let's see what's physically happening in the warehouse.

**Navigate to:** `http://MACHINE_IP` in your browser

### **What You'll See**

Through the security camera, you can see the warehouse floor in real-time:

- Robotic arms are busy at work
- Conveyor belts are running smoothly
- **But something is wrong:**
  - Pastel-colored chocolate eggs being sorted
  - Easter-themed packaging on the assembly line
  - Delivery drones loading eggs instead of gifts
  - Status display shows: **Compromised**

This visual confirmation tells you the problem is **real and active**. The system isn't broken—it's working perfectly, just delivering the wrong items. This is a classic sign of a **logic manipulation attack** rather than a system failure.

**Note:** Keep this CCTV feed open in a separate tab. It will update as you make changes to the system, providing real-time feedback on your remediation efforts.

---

## **Modbus Reconnaissance with Python**

We'll interrogate the Modbus server directly using Python and the **pymodbus** library.

**Note:** Remember that crumpled note you found earlier? Pull it out now. The terminology that seemed foreign—HR0, C11, "Modbus"—is about to make perfect sense.

### **Step 1: Install PyModbus**

On the AttackBox, it's already pre-installed. If using your own machine:

```bash
pip3 install pymodbus==3.6.8
```

---

### **Step 2: Establish Connection**

Open a Python interpreter:

```bash
python3
```

Connect to the PLC:

```python
from pymodbus.client import ModbusTcpClient

# Connect to the PLC on port 502
client = ModbusTcpClient('MACHINE_IP', port=502)

# Establish connection
if client.connect():
    print("Connected to PLC successfully")
else:
    print("Connection failed")
```

**Output:**

```
Connected to PLC successfully
```

**Excellent!** We have a connection to the PLC's Modbus interface. Notice how **no authentication was required**—this is a critical security weakness in the Modbus protocol.

---

### **Step 3: Reading Holding Registers**

Holding registers store numeric configuration values. According to the note, **HR0** controls package type selection.

#### **Read HR0 (Package Type)**

```python
# Read holding register 0 (Package Type)
result = client.read_holding_registers(address=0, count=1, slave=1)

if not result.isError():
    package_type = result.registers[0]
    print(f"HR0 (Package Type): {package_type}")
    if package_type == 0:
        print("  Christmas Presents")
    elif package_type == 1:
        print("  Chocolate Eggs")
    elif package_type == 2:
        print("  Easter Baskets")
```

**Output:**

```
HR0 (Package Type): 1
  Chocolate Eggs
```

**There it is!** HR0 is set to **1**, which means the system is configured to load chocolate eggs. The note was right—this is exactly what's causing the problem.

---

#### **Read HR1 (Delivery Zone)**

```python
# Read holding register 1 (Delivery Zone)
result = client.read_holding_registers(address=1, count=1, slave=1)

if not result.isError():
    zone = result.registers[0]
    print(f"HR1 (Delivery Zone): {zone}")
    if zone == 10:
        print("  WARNING: Ocean dump zone")
    else:
        print(f"  Normal delivery zone")
```

**Output:**

```
HR1 (Delivery Zone): 5
  Normal delivery zone
```

Zone 5 is normal—good news.

---

#### **Read HR4 (System Signature)**

```python
# Read holding register 4 (System Signature)
result = client.read_holding_registers(address=4, count=1, slave=1)

if not result.isError():
    signature = result.registers[0]
    print(f"HR4 (System Signature): {signature}")
    if signature == 666:
        print("  EGGSPLOIT DETECTED - King Malhare's signature")
```

**Output:**

```
HR4 (System Signature): 666
  EGGSPLOIT DETECTED - King Malhare's signature
```

The value **666** confirms this system has been compromised by the Eggsploit framework. This matches the taunt message: "**EGGSPLOIT v6.66**".

---

### **Step 4: Reading Coils**

Coils are boolean flags that control system behavior. The note mentioned several critical coils.

#### **Read C10 (Inventory Verification)**

```python
# Read coil 10 (Inventory Verification)
result = client.read_coils(address=10, count=1, slave=1)

if not result.isError():
    verification = result.bits[0]
    print(f"C10 (Inventory Verification): {verification}")
    if not verification:
        print("  DISABLED - System not checking stock")
```

**Output:**

```
C10 (Inventory Verification): False
  DISABLED - System not checking stock
```

Inventory verification is disabled. The system is blindly following commands without checking if items actually exist in stock.

---

#### **Read C11 (Protection/Override)**

```python
# Read coil 11 (Protection/Override)
result = client.read_coils(address=11, count=1, slave=1)

if not result.isError():
    protection = result.bits[0]
    print(f"C11 (Protection/Override): {protection}")
    if protection:
        print("  ACTIVE - Changes are being monitored")
```

**Output:**

```
C11 (Protection/Override): True
  ACTIVE - Changes are being monitored
```

**This is crucial!** C11 is enabled, which means the system is actively monitoring for changes.

**Remember the warning on the note:** "Never change HR0 while C11=True! Will trigger countdown!"

---

#### **Read C15 (Self-Destruct Status)**

```python
# Read coil 15 (Self-Destruct Status)
result = client.read_coils(address=15, count=1, slave=1)

if not result.isError():
    armed = result.bits[0]
    print(f"C15 (Self-Destruct Armed): {armed}")
    if not armed:
        print("  Not armed yet - safe for now")
```

**Output:**

```
C15 (Self-Destruct Armed): False
  Not armed yet - safe for now
```

**Good news** — the self-destruct isn't armed yet. But it will be if we try to change HR0 while C11 is active. This is the trap mechanism.

---

### **Step 5: Understanding the Trap**

Now the note makes complete sense. The maintenance technician discovered:

1. **HR0 is set to 1** (forcing eggs)
2. **C11 protection is enabled** (monitoring for changes)
3. **If anyone changes HR0 while C11 is True, C15 gets armed**
4. **Once C15 is armed, a 30-second countdown begins**
5. **After 30 seconds, C12 (Emergency Dump) activates, and everything dumps to Zone 10 (ocean)**

The technician's warning was trying to save whoever found this note from making things worse.

---

## 📝 Complete Reconnaissance Script

Let's create a comprehensive script to see the full system state.

Exit the Python interpreter (press `Ctrl+D` or type `exit()`), then create a new file:

```bash
nano reconnaissance.py
```

**Copy and paste the following code:**

```python
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

print("=" * 60)
print("TBFC Drone System - Reconnaissance Report")
print("=" * 60)
print()

# Read holding registers
print("HOLDING REGISTERS:")
print("-" * 60)

registers = client.read_holding_registers(address=0, count=5, slave=UNIT_ID)
if not registers.isError():
    hr0, hr1, hr2, hr3, hr4 = registers.registers

    print(f"HR0 (Package Type): {hr0}")
    print(f"  0=Christmas, 1=Eggs, 2=Baskets")
    print()

    print(f"HR1 (Delivery Zone): {hr1}")
    print(f"  1-9=Normal zones, 10=Ocean dump")
    print()

    print(f"HR4 (System Signature): {hr4}")
    if hr4 == 666:
        print(f"  WARNING: Eggsploit signature detected")
    print()

# Read coils
print("COILS (Boolean Flags):")
print("-" * 60)

coils = client.read_coils(address=10, count=6, slave=UNIT_ID)
if not coils.isError():
    c10, c11, c12, c13, c14, c15 = coils.bits[:6]

    print(f"C10 (Inventory Verification): {c10}")
    print(f"  Should be True")
    print()

    print(f"C11 (Protection/Override): {c11}")
    if c11:
        print(f"  ACTIVE - System monitoring for changes")
    print()

    print(f"C12 (Emergency Dump): {c12}")
    if c12:
        print(f"  CRITICAL: Dump protocol active")
    print()

    print(f"C13 (Audit Logging): {c13}")
    print(f"  Should be True")
    print()

    print(f"C14 (Christmas Restored): {c14}")
    print(f"  Auto-set when system is fixed")
    print()

    print(f"C15 (Self-Destruct Armed): {c15}")
    if c15:
        print(f"  DANGER: Countdown active")
    print()

print("=" * 60)
print("THREAT ASSESSMENT:")
print("=" * 60)

if hr4 == 666:
    print("Eggsploit framework detected")
if c11:
    print("Protection mechanism active - trap is set")
if hr0 == 1:
    print("Package type forced to eggs")
if not c10:
    print("Inventory verification disabled")
if not c13:
    print("Audit logging disabled")

print()
print("REMEDIATION REQUIRED")
print("=" * 60)

client.close()
```

**Save and exit** (`Ctrl+X`, then `Y`, then `Enter`). Run the script:

```bash
python3 reconnaissance.py
```

Now we have a **complete picture of the compromise**. Time to restore the system.

---

## **Safe Remediation**

Based on our reconnaissance, we need to:

1. Disable protection mechanism (**C11**) FIRST
2. Change package type to Christmas gifts (**HR0 = 0**)
3. Enable inventory verification (**C10 = True**)
4. Enable audit logging (**C13 = True**)
5. Verify **C15** never got armed

**The order is critical.** If we change HR0 before disabling C11, the trap triggers.

---

### **Create the Remediation Script**

```bash
nano restore_christmas.py
```

**Enter the following code:**

```python
#!/usr/bin/env python3
from pymodbus.client import ModbusTcpClient
import time

PLC_IP = "MACHINE_IP"
PORT = 502
UNIT_ID = 1

def read_coil(client, address):
    result = client.read_coils(address=address, count=1, slave=UNIT_ID)
    if not result.isError():
        return result.bits[0]
    return None

def read_register(client, address):
    result = client.read_holding_registers(address=address, count=1, slave=UNIT_ID)
    if not result.isError():
        return result.registers[0]
    return None

# Connect to PLC
client = ModbusTcpClient(PLC_IP, port=PORT)

if not client.connect():
    print("Failed to connect to PLC")
    exit(1)

print("=" * 60)
print("TBFC Drone System - Christmas Restoration")
print("=" * 60)
print()

# Step 1: Check current state
print("Step 1: Verifying current system state...")
time.sleep(1)

package_type = read_register(client, 0)
protection = read_coil(client, 11)
armed = read_coil(client, 15)

print(f"  Package Type: {package_type} (1 = Eggs)")
print(f"  Protection Active: {protection}")
print(f"  Self-Destruct Armed: {armed}")
print()

# Step 2: Disable protection
print("Step 2: Disabling protection mechanism...")
time.sleep(1)

result = client.write_coil(11, False, slave=UNIT_ID)
if not result.isError():
    print("  Protection DISABLED")
    print("  Safe to proceed with changes")
else:
    print("  FAILED to disable protection")
    client.close()
    exit(1)

print()
time.sleep(1)

# Step 3: Change package type to Christmas
print("Step 3: Setting package type to Christmas presents...")
time.sleep(1)

result = client.write_register(0, 0, slave=UNIT_ID)
if not result.isError():
    print("  Package type changed to: Christmas Presents")
else:
    print("  FAILED to change package type")

print()
time.sleep(1)

# Step 4: Enable inventory verification
print("Step 4: Enabling inventory verification...")
time.sleep(1)

result = client.write_coil(10, True, slave=UNIT_ID)
if not result.isError():
    print("  Inventory verification ENABLED")
else:
    print("  FAILED to enable verification")

print()
time.sleep(1)

# Step 5: Enable audit logging
print("Step 5: Enabling audit logging...")
time.sleep(1)

result = client.write_coil(13, True, slave=UNIT_ID)
if not result.isError():
    print("  Audit logging ENABLED")
    print("  Future changes will be logged")
else:
    print("  FAILED to enable logging")

print()
time.sleep(2)

# Step 6: Verify restoration
print("Step 6: Verifying system restoration...")
time.sleep(1)

christmas_restored = read_coil(client, 14)
new_package_type = read_register(client, 0)
emergency_dump = read_coil(client, 12)
self_destruct = read_coil(client, 15)

print(f"  Package Type: {new_package_type} (0 = Christmas)")
print(f"  Christmas Restored: {christmas_restored}")
print(f"  Emergency Dump: {emergency_dump}")
print(f"  Self-Destruct Armed: {self_destruct}")
print()

if christmas_restored and new_package_type == 0 and not emergency_dump and not self_destruct:
    print("=" * 60)
    print("SUCCESS - CHRISTMAS IS SAVED")
    print("=" * 60)
    print()
    print("Christmas deliveries have been restored")
    print("The drones will now deliver presents, not eggs")
    print("Check the CCTV feed to see the results")
    print()

    # Read the flag from registers
    flag_result = client.read_holding_registers(address=20, count=12, slave=UNIT_ID)
    if not flag_result.isError():
        flag_bytes = []
        for reg in flag_result.registers:
            flag_bytes.append(reg >> 8)
            flag_bytes.append(reg & 0xFF)
        flag = ''.join(chr(b) for b in flag_bytes if b != 0)
        print(f"Flag: {flag}")

    print()
    print("=" * 60)
else:
    print("Restoration incomplete - check system state")

client.close()
print()
print("Disconnected from PLC")
```

**Save and exit.** Run the restoration script:

```bash
python3 restore_christmas.py
```

**Expected Output:**

```
============================================================
SUCCESS - CHRISTMAS IS SAVED
============================================================

Christmas deliveries have been restored
The drones will now deliver presents, not eggs
Check the CCTV feed to see the results

Flag: THM{eGgMas0V3r}

============================================================

Disconnected from PLC
```

**Excellent work!** Now check the CCTV feed at `http://MACHINE_IP`—you should see King Malhare's defeat displayed.

---

## **What If You Triggered the Trap?**

If you had tried to change HR0 **before** disabling C11, here's what would have happened:

1. **C15 (Self-Destruct)** would arm immediately
2. A **30-second countdown** would begin
3. After 30 seconds, **C12 (Emergency Dump)** would activate
4. **HR1** would change to 10 (ocean zone)
5. All remaining inventory would be dumped
6. The CCTV would show the trap activation screen
7. You would need to restart the challenge

This demonstrates why understanding industrial control systems before making changes is critical. In real-world scenarios, triggering safety mechanisms or traps could have **severe physical consequences**.

---

## **Post-Incident Analysis**

**King Malhare's attack was sophisticated because it:**

- Used **unauthenticated Modbus access** (port 502)
- Manipulated configuration values directly at the protocol level
- Disabled safety mechanisms (verification, logging)
- Implemented a **trap** to prevent easy remediation
- Left a **signature** (666) as a calling card

The maintenance technician who left the note likely discovered the compromise but was interrupted before they could fix it. Their documentation **saved Christmas** by warning about the trap mechanism.

**Congratulations!** You've successfully investigated and remediated an industrial control system compromise. You've learned:

- How **SCADA systems** work
- How **PLCs** operate
- How **Modbus communication** functions
- How attackers can manipulate these systems
- How to **safely restore** them

---

## **Questions**

### **What's the flag?**

<details>
  <summary>Click to reveal the answer</summary>

**THM{eGgMas0V3r}**

</details>

---
