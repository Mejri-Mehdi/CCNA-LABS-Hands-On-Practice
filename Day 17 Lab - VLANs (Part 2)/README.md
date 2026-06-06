## What I Learned

In this lab, I configured **inter‑VLAN routing using a single router physical interface** (router‑on‑a‑stick) with 802.1Q trunking. The topology included two switches (SW1, SW2) and a router (R1).

### VLANs and subnets

| VLAN | Subnet         | Mask | Gateway (last usable) | PC example |
|------|----------------|------|----------------------|-------------|
| 10   | 10.0.0.0       | /26  | 10.0.0.62            | PC1         |
| 30   | (implied)      | /26  | (last usable)        | PC4         |

### Tasks performed

1. **Configured switch access ports** – ports connected to PCs were set as `access` ports and assigned to the correct VLAN (`vlan 10` or `vlan 30`).

2. **Configured trunk between SW1 and SW2**:
   - Interface mode `trunk` with `switchport trunk encapsulation dot1q`.
   - Allowed **only the necessary VLANs** (e.g., VLAN 10 and VLAN 30) using `switchport trunk allowed vlan 10,30`.
   - Set an **unused VLAN** (e.g., VLAN 999) as the **native VLAN** (`switchport trunk native vlan 999`) – this eliminates untagged traffic on the trunk.

3. **Ensured VLANs exist on both switches** – created VLAN 10 and VLAN 30 on SW1 and SW2 (also the unused native VLAN if needed).

4. **Configured router‑on‑a‑stick** between SW2 and R1:
   - On R1: created subinterfaces for each VLAN (e.g., `G0/0.10` and `G0/0.30`).
   - Enabled 802.1Q encapsulation (`encapsulation dot1q 10` and `encapsulation dot1q 30`).
   - Assigned the **last usable IP address** of each subnet as the gateway on the subinterface.
   - Physical interface `G0/0` had **no IP address** and was left `no shutdown`.

5. **Tested connectivity** – pings between PCs in different VLANs succeeded (e.g., PC1 → PC4), proving inter‑VLAN routing worked.

### Key takeaways

- **Router‑on‑a‑stick** uses one router physical link for multiple VLANs via subinterfaces and 802.1Q tagging.
- **Trunk allowed VLAN list** improves security and reduces unnecessary broadcast traffic.
- **Native VLAN** should be set to an unused VLAN to prevent VLAN hopping attacks and keep management clean.
- **Last usable address** as gateway follows the lab’s addressing convention.
- Without the trunk between switches, VLANs would be isolated to each switch; the trunk extends them across both.

This lab deepened my understanding of VLAN trunking, router subinterfaces, and efficient inter‑VLAN design.