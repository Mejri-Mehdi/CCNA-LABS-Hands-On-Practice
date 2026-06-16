## What I Learned

In this lab, I configured IPv6 addressing on routers using **EUI‑64** (Extended Unique Identifier) format, enabled IPv6 on interfaces without explicitly assigning a global address, and configured **IPv6 static routes** to enable communication between PCs.

### Network information

| Device | Interface | IPv6 Network | Role |
|--------|-----------|--------------|------|
| R1 G0/1 | SW1 LAN | 2001:db8:0:1::/64 | PC1 gateway |
| R1 G0/0 | R1↔R2 | 2001:db8::/64 | Point‑to‑point link |
| R2 G0/0 | R1↔R2 | 2001:db8::/64 | Point‑to‑point link |
| R2 G0/1 | SW2 LAN | 2001:db8:0:1::/64 | PC2 gateway |

All interfaces were already enabled and had IPv4 configured. This lab focused on adding **IPv6** to create a dual‑stack network.

### Tasks performed

1. **Configured IPv6 addresses on G0/1 of R1 and R2 using EUI‑64**:
   - EUI‑64 generates the interface ID from the **MAC address**:
     - Insert `FFFE` in the middle (after the 24th bit).
     - Flip the 7th bit (Universal/Local bit).
   - Command: `ipv6 address 2001:db8:0:1::/64 eui-64` (for R1) and `ipv6 address 2001:db8:0:2::/64 eui-64` (for R2).
   - **Before configuring, I calculated the expected interface IDs** based on the MAC address of each interface.

2. **Enabled IPv6 on G0/0 of R1/R2 without explicitly configuring an IPv6 address**:
   - Command: `ipv6 enable` – this generates a **link‑local address** (FE80::/10) but no global unicast address.
   - The link‑local addresses are used for neighbour discovery and routing updates.
   - (Note: Later, when configuring static routes, I needed to specify the **next‑hop link‑local** or use the global address if configured.)

3. **Configured IPv6 static routes on R1 and R2** to enable PC1 to ping PC2:
   - On **R1**: `ipv6 route 2001:db8:0:2::/64 2001:db8::2` (or via link‑local: `ipv6 route 2001:db8:0:2::/64 g0/0 FE80::...`)
   - On **R2**: `ipv6 route 2001:db8:0:1::/64 2001:db8::1`
   - Used `ipv6 route ?` to learn the syntax – options include **next‑hop address**, **exit interface**, or **interface + next‑hop**.

4. **Verified connectivity** – PC1 ping to PC2 over IPv6 succeeded.

### Key takeaways

- **EUI‑64** automatically generates a 64‑bit interface ID from the MAC address – saves manual addressing.
- **`ipv6 enable`** activates IPv6 on an interface and generates a link‑local address without a global unicast address.
- **IPv6 static routes** require the destination network and either:
  - Next‑hop IPv6 address
  - Exit interface
  - Or both (for multi‑access links)
- **Link‑local addresses** are only valid on the same link; when using them as next‑hop, the exit interface must also be specified.

### Verification commands

show ipv6 interface brief
show ipv6 route
ping 2001:db8:0:2::2

This lab deepened my understanding of EUI‑64 addressing, link‑local usage, and IPv6 static routing.