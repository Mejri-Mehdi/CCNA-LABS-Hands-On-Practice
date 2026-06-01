## What I Learned

In this lab, I configured a router (R1) with multiple interfaces and verified connectivity between PCs in different subnets.

### Tasks performed

1. **Configured R1’s hostname** – `hostname R1` in global configuration mode.
2. **Viewed interface list** – used `show ip interface brief` to see interfaces, IP addresses, and status.
3. **Assigned IP addresses and enabled interfaces** – on `G0/0`, `G0/1`, and `G0/2`:
   - `interface g0/0` → `ip address 15.0.0.1 255.0.0.0` → `no shutdown`
   - `interface g0/1` → `ip address 182.98.0.1 255.255.0.0` → `no shutdown`
   - `interface g0/2` → `ip address 201.191.20.1 255.255.255.0` → `no shutdown`
   - Added descriptions (e.g., `description Link to SW1`) for documentation.
4. **Verified interface configuration again** – `show ip interface brief` and `show running-config`.
5. **Saved configuration** – `write memory` or `copy running-config startup-config`.
6. **Configured PC IP addresses** – set static IPs, subnet masks, and default gateways on PC1, PC2, PC3.
7. **Tested connectivity with pings**:
   - `PC1` (15.0.0.?) ping to `PC2` (182.98.0.?) → successful.
   - `PC1` ping to `PC3` (201.191.20.?) → successful (see sample ping replies with TTL=127).

### Sample ping output observed

Pinging 182.98.0.1 with 32 bytes of data:
Reply from 182.98.0.1: bytes=32 time=1ms TTL=127
Reply from 182.98.0.1: bytes=32 time=1ms TTL=127


### Key takeaways

- **`show ip interface brief`** quickly displays all interfaces, IPs, and operational status.
- **`no shutdown`** enables an interface (by default, Cisco router interfaces are administratively down).
- **Interface descriptions** improve documentation and troubleshooting.
- **Default gateway** on PCs must point to the router’s interface IP in the same subnet.
- **Ping with TTL=127** indicates one router hop (TTL starts at 128 on Windows, decreased by 1 per router).

This lab reinforced basic router configuration, interface management, and end‑to‑end IP connectivity across multiple subnets.
