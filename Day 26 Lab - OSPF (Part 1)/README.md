## What I Learned

In this lab, I configured OSPF across multiple routers, set up loopback interfaces, and then made R1 an **ASBR** (Autonomous System Boundary Router) that injects a **default route** into the OSPF domain.

### Tasks performed

1. **Basic configuration** – hostnames, IP addresses, and router interfaces enabled (`no shutdown`).  
   (ISP R1 was **not** configured – just connected.)

2. **Loopback interfaces** – created as `/32` addresses (e.g., `1.1.1.1/32` on R1, `2.2.2.2/32` on R2, `3.3.3.3/32` on R3, `4.4.4.4/32` on R4).

3. **OSPF configuration** on each router:
   - `router ospf 1` (process ID can vary)
   - `network` statements to include all required interfaces – **except** R1’s Internet link (toward ISPR1) – that interface was left out of OSPF.
   - **Passive interfaces** configured where no OSPF neighbours exist (e.g., loopback interfaces, LAN‑facing interfaces like `G0/0` on R4 toward `192.168.4.0/24`).

4. **Configured R1 as an ASBR** to advertise a default route into OSPF:
   - Added a static default route on R1 pointing to ISP: `ip route 0.0.0.0 0.0.0.0 203.0.113.2`
   - Redistributed the default route into OSPF with `default-information originate` (or `redistribute static subnets` depending on IOS version).
   - R1 now becomes an ASBR.

5. **Checked routing tables** of R2, R3, and R4:
   - Each non‑ASBR router received a default route (`0.0.0.0/0`) via OSPF.
   - The next‑hop points toward R1 (or the shortest path through the OSPF area).
   - Verified with `show ip route ospf` or `show ip route`.

### Network diagram summary

| Link | Subnet | Area |
|------|--------|------|
| R1 ↔ ISPR1 | 203.0.113.0/30 | Not in OSPF |
| R1 ↔ R2 | 10.0.12.0/30 | Area 0 |
| R1 ↔ R3 | 10.0.13.0/30 | Area 0 |
| R2 ↔ R4 | 10.0.24.0/30 | Area 0 |
| R3 ↔ R4 | 10.0.34.0/30 | Area 0 |
| R4 LAN | 192.168.4.0/24 | Area 0 (passive) |

### Key takeaways

- **ASBR** is a router that connects the OSPF domain to an external network (like the Internet).
- **`default-information originate`** injects a default route into OSPF – other routers learn it automatically.
- **Passive interfaces** on loopbacks and LANs prevent unnecessary OSPF hellos and adjacency attempts.
- Excluding the Internet link from OSPF is correct – we want external routes redistributed, not the link itself as an OSPF network.
- All internal routers (R2, R3, R4) receive the default route and can reach external destinations via R1.

### Verification commands

show ip route ospf
show ip route 0.0.0.0
show ip ospf border-routers
show ip ospf interface

This lab demonstrated how to connect an OSPF network to the Internet using an ASBR and default route redistribution.