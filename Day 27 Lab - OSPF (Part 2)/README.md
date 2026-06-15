# OSPF Lab – Reference Bandwidth, ASBR Default Route & Hello Messages – Packet Tracer

## What I Learned

In this lab, I configured OSPF across multiple routers (R1, R2, R3, R4) in Area 0, set the reference bandwidth so FastEthernet interfaces have a cost of 100, and made R1 an ASBR injecting a default route. I also analyzed OSPF Hello messages using Simulation Mode.

### Network diagram summary

| Link | Subnet | OSPF Area |
|------|--------|-----------|
| R1 ↔ ISP1 | 203.0.113.0/30 | Not in OSPF (outside) |
| R1 ↔ R2 | 10.0.12.0/30 | Area 0 |
| R1 ↔ R3 | 10.0.13.0/30 | Area 0 |
| R2 ↔ R4 | 10.0.24.0/30 | Area 0 |
| R3 ↔ R4 | 10.0.34.0/30 | Area 0 |
| R4 LAN (SW1) | 192.168.4.0/24 | Area 0 (passive) |

### Tasks performed

1. **Basic configuration** – hostnames, IP addresses, and `no shutdown` on all router interfaces (ISP1 was **not** configured).

2. **Loopback interfaces** – `/32` addresses: `1.1.1.1/32` (R1), `2.2.2.2/32` (R2), `3.3.3.3/32` (R3), `4.4.4.4/32` (R4).

3. **OSPF enabled directly on each interface** (instead of `network` statements):
   - `interface <int>` → `ip ospf 1 area 0`
   - **Passive interfaces** configured where no OSPF neighbours exist (loopbacks, LAN interface on R4, and possibly R1’s Internet link) using `passive-interface <int>`.

4. **Reference bandwidth configuration** (so FastEthernet cost = 100):
   - Global command: `auto-cost reference-bandwidth 10000` (since 10000 Mbps / 100 Mbps = 100 cost for FastEthernet).
   - Verified with `show ip ospf interface fastEthernet 0/0` (cost should be 100).

5. **Configured R1 as an ASBR** to advertise a default route into OSPF:
   - Static default route toward ISP1: `ip route 0.0.0.0 0.0.0.0 203.0.113.2`
   - Redistributed into OSPF: `default-information originate` (or `redistribute static subnets`).
   - R1 becomes an ASBR.

6. **Checked R4’s routing table** – it received a default route (`0.0.0.0/0`) via OSPF, with next‑hop pointing toward R1 (or the best path through Area 0).  
   - Command: `show ip route ospf` or `show ip route 0.0.0.0`.

7. **Used Simulation Mode** to capture OSPF Hello messages between routers.  
   **Fields included in a Hello message:**
   - Router ID
   - Area ID
   - Authentication type & data (if configured)
   - Hello interval (default 10 seconds)
   - Dead interval (default 40 seconds)
   - Network mask
   - Options (e.g., E‑bit for external routing)
   - Router priority (for DR/BDR election)
   - Designated Router (DR)
   - Backup Designated Router (BDR)
   - Neighbor list (only seen in hellos sent after adjacency forms? Actually hellos contain a list of known neighbors)

### Key takeaways

- **Reference bandwidth** changes OSPF cost calculation; set globally to make all interface costs consistent with link speed.
- **Interface‑specific OSPF enablement** (`ip ospf 1 area 0`) is an alternative to `network` statements – cleaner for point‑to‑point links.
- **Passive interfaces** prevent OSPF hellos on loops and LANs – still advertise the subnet.
- **ASBR default route injection** allows internal routers to reach external networks via a single exit point.
- **OSPF Hello messages** are multicast to `224.0.0.5` (all OSPF routers) and contain critical parameters – mismatches prevent adjacency.

### Verification commands

show ip ospf interface
show ip ospf neighbor
show ip route ospf
show ip route 0.0.0.0

This lab deepened my understanding of OSPF cost tuning, passive interfaces, default route redistribution, and the content of OSPF Hello messages.