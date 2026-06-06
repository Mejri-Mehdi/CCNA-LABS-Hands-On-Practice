## What I Learned

In this lab, I replaced the **Router‑on‑a‑stick (ROAS)** configuration between R1 and SW2 with a **point‑to‑point Layer 3 connection**. I then configured **SVIs (Switch Virtual Interfaces)** on SW2 to handle inter‑VLAN routing internally.

### Network overview

| VLAN | Subnet         | Mask | Gateway (last usable) |
|------|----------------|------|----------------------|
| 10   | 10.0.0.0       | /26  | 10.0.0.62            |
| 20   | 10.0.0.64      | /26  | 10.0.0.126           |
| 30   | 10.0.0.128     | /26  | 10.0.0.190           |
| P2P  | 10.0.0.192     | /30  | –                    |

- **SW1** and **SW2** are connected via a **trunk** (carrying VLANs 10,20,30).
- **R1 G0/0** has IP `10.0.0.193/30`.
- **SW2** connects to R1 via a Layer 3 point‑to‑point link (no subinterfaces).

### Tasks performed

1. **Removed ROAS** – deleted subinterfaces on R1 and the trunk configuration on the SW2 port facing R1.
2. **Configured point‑to‑point Layer 3 connection**:
   - On R1 G0/0: `ip address 10.0.0.193 255.255.255.252`, `no shutdown`.
   - On SW2: converted the interface to a **Layer 3 port** (`no switchport`), then `ip address 10.0.0.194 255.255.255.252`.
3. **Created SVIs on SW2** (one per VLAN):
   - `interface vlan 10` → `ip address 10.0.0.62 255.255.255.192` (last usable)
   - `interface vlan 20` → `ip address 10.0.0.126 255.255.255.192`
   - `interface vlan 30` → `ip address 10.0.0.190 255.255.255.192`
   - Enabled each SVI with `no shutdown`.
4. **Configured default route on SW2**:
   - `ip route 0.0.0.0 0.0.0.0 10.0.0.193` (pointing to R1’s G0/0 IP).
5. **Tested inter‑VLAN connectivity** – pings between PCs in different VLANs succeeded (routed by SW2’s SVIs).
6. **Tested Internet connectivity** – from any PC, ping `1.1.1.1` (R1 and the Internet router already had routes configured).

### Key takeaways

- **Layer 3 switch** with SVIs can route between VLANs internally – faster and more scalable than ROAS.
- **Point‑to‑point Layer 3 link** between router and switch uses `/30` subnet and `no switchport` on the switch side.
- **Default route** on the Layer 3 switch sends all unknown traffic upstream to the router (and then to the Internet).
- **SVI** acts as the gateway for its VLAN; the switch must have IP routing enabled (`ip routing`).
- **Last usable address** convention applies to SVIs as the default gateway for hosts.

This lab transitioned my understanding from external router‑based inter‑VLAN routing to internal switching‑based routing using SVIs.

<img width="927" height="669" alt="Topology Screenshot" src="https://github.com/user-attachments/assets/ea966e68-e8c1-4ecd-93c3-ecabaabcbc24" />
