## What I Learned

In this lab, I worked with a network of four switches (SW1, SW2, SW3, SW4) running **PVST+** (Per‑VLAN Spanning Tree). Two VLANs were used: **VLAN1** and **VLAN2**. I manipulated STP behaviour by configuring root bridges, changing interface costs, adjusting port priorities, and enabling security features.

### Initial topology (simplified)

- SW1, SW2, SW3, SW4 connected in a loop (likely a square or triangle).
- PC1 and PC2 attached to SW3/SW4 in VLAN2 (subnet 172.16.0.0/25).
- Default STP election elected a root bridge (initially SW2 according to task 1).

### Tasks performed

1. **Checked initial STP topology** using `show spanning-tree`:
   - Identified current root bridge (SW2).
   - Recorded port roles (Root/Designated/Alternate) and states (forwarding/blocking) on each switch.

2. **Configured root bridges per VLAN**:
   - **VLAN1**: primary root = SW1, secondary root = SW2.
   - **VLAN2**: primary root = SW2, secondary root = SW1.
   - Commands: `spanning-tree vlan 1 root primary`, `spanning-tree vlan 1 root secondary` (and similarly for VLAN2).
   - Verified new root placement and port role changes.

3. **Changed interface cost** – increased VLAN1 cost on SW4’s F0/2 to 100:
   - `interface f0/2` → `spanning-tree vlan 1 cost 100`.
   - Checked if SW4 selected a different root port for VLAN1 – **yes**, because the path cost to root increased, making another port (if available) lower cost.

4. **Changed port priority** – set VLAN1 port priority of SW1’s F0/1 to 240:
   - `interface f0/1` → `spanning-tree vlan 1 port-priority 240`.
   - Checked if SW3 selected a different root port – **no**, because port priority only influences which port on the **same switch** is chosen when multiple ports lead to the same root bridge (tie‑breaker). It does not affect another switch’s root port selection.

5. **Configured PortFast and BPDU Guard** on access ports (F0/3 of SW3 and SW4):
   - `interface f0/3` → `spanning-tree portfast` (or `spanning-tree portfast default` globally).
   - `interface f0/3` → `spanning-tree bpduguard enable`.
   - This immediately transitions the port to forwarding (no STP listening/learning) and shuts it down if a BPDU is received (prevents rogue switch loops).

### Key takeaways

- **PVST+** runs a separate STP instance per VLAN – root bridges can be different per VLAN for load sharing.
- **`root primary`** sets priority to 24576 (or lower than current root); **`root secondary`** sets priority to 28672.
- **Interface cost** influences root port selection – higher cost makes a path less desirable.
- **Port priority** is a tie‑breaker **on the same switch** when two ports have equal cost to root – does not affect neighbours’ elections.
- **PortFast** speeds up host connectivity (skips listening/learning) – only safe on access ports.
- **BPDU Guard** shuts down a PortFast port if a BPDU arrives – protects against accidental switch connections.

This lab gave me fine‑grained control over STP behaviour and enhanced network stability and security.

<img width="626" height="615" alt="Topology Screenshot" src="https://github.com/user-attachments/assets/7eb00135-9e94-4718-977e-80b79fff6b45" />
