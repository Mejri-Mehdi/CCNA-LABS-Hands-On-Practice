## What I Learned

In this lab, I took an existing IPv4 network and added IPv6 addressing to create a **dual‑stack** network (IPv4 and IPv6 running simultaneously). All IPv4 configurations were already complete.

### Network information

| Subnet | IPv4 Network | IPv6 Network |
|--------|--------------|--------------|
| SW1 LAN (PC1) | 192.168.1.0/24 | 2001:DB8:0:1::/64 |
| SW2 LAN (PC2) | 192.168.2.0/24 | 2001:DB8:0:2::/64 |
| SW3 LAN (PC3) | 192.168.3.0/24 | 2001:DB8:0:3::/64 |

R1 connects to all three subnets (G0/0, G0/1, G0/2).

### Tasks performed

1. **Enabled IPv6 routing on R1** – global command: `ipv6 unicast-routing` (required for the router to forward IPv6 packets).

2. **Configured IPv6 addresses on R1 interfaces**:
   - `interface g0/0` → `ipv6 address 2001:DB8:0:1::1/64`
   - `interface g0/1` → `ipv6 address 2001:DB8:0:2::1/64`
   - `interface g0/2` → `ipv6 address 2001:DB8:0:3::1/64`
   - (Alternatively, used `ipv6 address 2001:DB8:0:1::1/64` – statically assigned.)

3. **Confirmed configurations** – used `show ipv6 interface brief` to list all IPv6 addresses on each interface.  
   Each interface showed its link‑local address (FE80::/10) and the configured global unicast address.

4. **Configured IPv6 on each PC**:
   - PC1: `2001:DB8:0:1::2/64`, gateway `2001:DB8:0:1::1`
   - PC2: `2001:DB8:0:2::2/64`, gateway `2001:DB8:0:2::1`
   - PC3: `2001:DB8:0:3::2/64`, gateway `2001:DB8:0:3::1`

5. **Tested connectivity** – pinged between PCs using **both IPv4 and IPv6**:
   - IPv4 ping: `ping 192.168.2.2` (PC1 → PC2) – successful.
   - IPv6 ping: `ping 2001:DB8:0:2::2` (PC1 → PC2) – successful.

### Key takeaways

- **`ipv6 unicast-routing`** is essential on routers to enable IPv6 forwarding.
- **Stateless autoconfiguration** (SLAAC) is possible, but this lab used **static IPv6 assignment**.
- **Link‑local addresses** (FE80::/10) are automatically generated on every IPv6‑enabled interface.
- **Dual‑stack** means both IPv4 and IPv6 run concurrently – applications can use either protocol.
- **Ping** works the same way for IPv6, but the address format uses colons.

### Verification commands

show ipv6 interface brief
show ipv6 route
ping 2001:DB8:0:2::2
ping 192.168.2.2

This lab introduced IPv6 addressing, configuration, and dual‑stack operation in a simple routed network.

<img width="527" height="665" alt="Toplogy Screenshot" src="https://github.com/user-attachments/assets/8260921a-5539-4aa2-bc20-a8d121a0c19c" />
