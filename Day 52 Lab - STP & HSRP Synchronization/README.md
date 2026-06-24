## What I Learned

In this lab, I configured **HSRP (Hot Standby Router Protocol)** on DSW1 and DSW2 to provide gateway redundancy for VLANs 10 and 20. I also ensured proper **synchronization with STP** so the active HSRP router for each VLAN is also the STP root bridge for that VLAN.

### Network overview

| Device | VLAN10 SVI | VLAN20 SVI | Role |
|--------|------------|------------|------|
| DSW1 | 10.0.10.1/24 | 10.0.20.1/24 | Distribution switch |
| DSW2 | 10.0.10.2/24 | 10.0.20.2/24 | Distribution switch |
| ASW1 | – | – | Access switch (PC1 in VLAN10) |
| ASW2 | – | – | Access switch (PC2 in VLAN20) |

- **SVIs (VLAN10/20)** are already pre‑configured on DSW1 and DSW2.
- **Trunks** connect DSW1/DSW2 to ASW1/ASW2.
- **VLANs 10 and 20** are configured on all switches.

### Tasks performed

#### 1. Configured HSRP on DSW1 and DSW2

**VLAN10 (Active: DSW1, Standby: DSW2):**
- **On DSW1:**
  - `interface vlan 10` → `standby version 2`
  - `standby 10 ip 10.0.10.254` (virtual IP)
  - `standby 10 priority 150` (higher than default 100)
  - `standby 10 preempt`
- **On DSW2:**
  - `interface vlan 10` → `standby version 2`
  - `standby 10 ip 10.0.10.254`
  - `standby 10 priority 90` (lower than DSW1)
  - `standby 10 preempt` (optional)

**VLAN20 (Active: DSW2, Standby: DSW1):**
- **On DSW2:**
  - `interface vlan 20` → `standby version 2`
  - `standby 20 ip 10.0.20.254`
  - `standby 20 priority 150`
  - `standby 20 preempt`
- **On DSW1:**
  - `interface vlan 20` → `standby version 2`
  - `standby 20 ip 10.0.20.254`
  - `standby 20 priority 90`
  - `standby 20 preempt`

#### 2. Synchronized HSRP with STP

**VLAN10:**
- **STP root bridge** should be **DSW1** (the HSRP active router).
- On **DSW1**: `spanning-tree vlan 10 root primary` (sets priority to 24576).
- On **DSW2**: `spanning-tree vlan 10 root secondary` (sets priority to 28672).

**VLAN20:**
- **STP root bridge** should be **DSW2** (the HSRP active router).
- On **DSW2**: `spanning-tree vlan 20 root primary`
- On **DSW1**: `spanning-tree vlan 20 root secondary`

#### 3. Verified configurations

- **HSRP status:** `show standby brief`
  - VLAN10: DSW1 = Active, DSW2 = Standby
  - VLAN20: DSW2 = Active, DSW1 = Standby
- **STP status:** `show spanning-tree vlan 10` and `show spanning-tree vlan 20`
  - Root bridge matches the HSRP active router for each VLAN.

### Key takeaways

- **HSRP** provides gateway redundancy – the virtual IP is shared; the active router forwards traffic.
- **STP root bridge** should match the HSRP active router to optimize traffic flow (avoid suboptimal paths).
- **Priority** determines HSRP active election (higher is better, default = 100).
- **Preemption** allows a higher‑priority router to reclaim the active role after a failure.
- **Synchronization** ensures that traffic follows the optimal path: the active gateway is also the root bridge, so Layer 2 and Layer 3 paths are aligned.
- **Root primary** sets the bridge priority to 24576; **root secondary** sets it to 28672 (ensuring it becomes root only if the primary fails).

### Verification commands

show standby brief
show standby
show spanning-tree vlan 10
show spanning-tree vlan 20
show ip interface brief

This lab demonstrated how to align HSRP and STP for optimal traffic flow and redundancy in a multi‑VLAN network.