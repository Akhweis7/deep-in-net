# Deep-in-Net — Labs Progress Report (Exercises 1–3)

**Student:** Amro Khweis  
**Tool:** Cisco Packet Tracer 8.x  
**Files:** `deep-in-net_ex01.pkt`, `deep-in-net_ex02.pkt`, `deep-in-net_ex03.pkt`

---

## Overview

This document records **what I built** and **how I built it** step by step for the first three exercises:

1. **Exercise 1 — Physical Topology (LAN with a Switch)** ✅ *Finished*
2. **Exercise 2 — IP Addressing & Ping (Logical Connectivity)** ✅ *Finished*
3. **Exercise 3 — Interconnecting Two LANs with a Router** ✅ *Finished*

At the end of each exercise, I ran tests and recorded expected results.

---

## Exercise 1 — Build a Basic LAN (PCs + Switch)

### 🎯 Goal
Create a simple physical network of six PCs connected to one switch with the correct cable type and verify link status.

### 🔧 Devices Used
- 6 × **PC** (End Devices)
- 1 × **Switch** (e.g., *2960* or *2950-24*)
- **Copper Straight-Through** cables (PC ↔ Switch)

### 🪜 Steps
1. From the bottom device bar, dragged **6 PCs** onto the workspace.
2. From **Switches**, dragged **one 2960 switch** to the center.
3. Clicked **Connections (⚡)** → chose **Copper Straight-Through**.
4. Connected each **PC FastEthernet0** to a **unique switch port** (Fa0/1 … Fa0/6).
5. Waited a few seconds for **link lights to turn green**.

### ✅ Test (“We are testing Exercise 1”)
- Visual check: All links showed **green** on both ends.

### 🏁 Status
**We have finished Exercise 1.**

---

## Exercise 2 — Assign IPs & Verify with Ping

### 🎯 Goal
Give each PC a unique IP in the same subnet and confirm end-to-end connectivity using `ping`.

### 🌐 Address Plan (LAN 192.168.1.0/24)
| PC | IP Address     | Subnet Mask     |
|----|-----------------|-----------------|
| PC0| 192.168.1.1     | 255.255.255.0   |
| PC1| 192.168.1.2     | 255.255.255.0   |
| PC2| 192.168.1.3     | 255.255.255.0   |
| PC3| 192.168.1.4     | 255.255.255.0   |
| PC4| 192.168.1.5     | 255.255.255.0   |
| PC5| 192.168.1.6     | 255.255.255.0   |

> No default gateway at this stage (no router yet).

### 🪜 Steps
1. On each PC: **Desktop → IP Configuration**.
2. Entered IP + Subnet Mask from the table above.

### ✅ Tests (“We are testing Exercise 2”)
From **PC0 → Command Prompt**:
```bash
ping 192.168.1.2
ping 192.168.1.6
```
**Expected:** `Reply from ... bytes=32 time<1ms TTL=128`

### 🏁 Status
**We have finished Exercise 2.**

---

## Exercise 3 — Two LANs + One Router (Inter-VLAN Routing)

### 🎯 Goal
Split the 6 PCs into two separate LANs, connect each LAN to a router, configure interfaces, set gateways on PCs, and verify cross-network communication.

### 🧱 Final Topology (ASCII)
```
   LAN 1 (192.168.1.0/24)              LAN 2 (192.168.2.0/24)
 PC0   PC1   PC2                        PC3   PC4   PC5
  |     |     |                          |     |     |
  +-----+-----+                        +--+-----+-----+
        Switch0 -------------------- Router -------------------- Switch1
            (to G0/0)             (G0/0) (G0/1)                 (to G0/1)
```

### 🌐 Address Plans
**LAN 1 (Switch0 → Router G0/0)**
- PCs: `192.168.1.1–192.168.1.3 / 255.255.255.0`
- Router G0/0: `192.168.1.254 / 255.255.255.0`

**LAN 2 (Switch1 → Router G0/1)**
- PCs: `192.168.2.1–192.168.2.3 / 255.255.255.0`
- Router G0/1: `192.168.2.254 / 255.255.255.0`

### 🪜 Steps
1. **Split devices into two groups:**
   - Switch0: PC0, PC1, PC2
   - Switch1: PC3, PC4, PC5
2. **Cabling:**
   - PC ↔ Switch: **Copper Straight-Through**
   - Switch0 ↔ Router **G0/0**: **Straight-Through**
   - Switch1 ↔ Router **G0/1**: **Straight-Through**
3. **Router Configuration (CLI):**
   ```
   enable
   configure terminal
   interface gigabitEthernet0/0
     ip address 192.168.1.254 255.255.255.0
     no shutdown
   exit
   interface gigabitEthernet0/1
     ip address 192.168.2.254 255.255.255.0
     no shutdown
   exit
   end
   write
   ```
   > After `no shutdown`, interfaces should show **UP/UP** (green links).
4. **Default Gateways on PCs:**
   - LAN 1 PCs → `Default Gateway = 192.168.1.254`
   - LAN 2 PCs → `Default Gateway = 192.168.2.254`

### ✅ Tests (“We are testing Exercise 3”)
- From **PC0 (192.168.1.1)**:
  ```bash
  ping 192.168.2.1
  ```
- From **PC3 (192.168.2.1)**:
  ```bash
  ping 192.168.1.1
  ```
**Expected:** `Reply from ... bytes=32 time<1ms TTL=128` both directions.

### 🏁 Status
**We have finished Exercise 3.**

---

## Notes & Troubleshooting

- If router CLI shows `Invalid input detected`, ensure you entered:
  - `enable` → `configure terminal` before interface commands.
- If `ping` fails across LANs:
  - Check that router interfaces have IPs and `no shutdown`.
  - Verify **Default Gateway** on each PC.
  - Confirm all Subnet Masks are `255.255.255.0`.
  - Wait a few seconds for links to turn **green** after cabling.
- Keep each exercise in a separate file:
  - `ex01.pkt` → physical topology
  - `ex02.pkt` → IP addressing + ping
  - `ex03.pkt` → two LANs + router

---

## Next (Exercise 4 — OSI Model Primer)

- Map what happens to a `ping` at each OSI layer.
- Observe ARP (Layer 2), IP (Layer 3), and ICMP in **Simulation Mode** in Packet Tracer.
- Capture and explain PDU flow between PCs across the router.
