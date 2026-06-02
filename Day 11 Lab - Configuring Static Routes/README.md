## What I Learned

In this lab, I configured two routers (R1 and R2) with static routes to enable end‑to‑end connectivity between PCs in different subnets.

### Network diagram summary

- **PC1** (192.168.1.0/24) connected to **SW1** → **R1 G0/1**
- **R1 G0/0** (192.168.12.0/24) connected to **R2 G0/0**
- **R2 G0/1** (192.168.3.0/24) connected to **SW2** → **PC2** (192.168.3.1)

### Tasks performed

1. **Configured PCs and routers** (no pre‑configurations):
   - Hostnames: `R1`, `R2`
   - IP addresses on all router interfaces (G0/0, G0/1) and PCs
   - Default gateway on PCs (pointing to the respective router interface)
   - Switches required no configuration.

2. **Configured static routes on the routers**:
   - On **R1**: `ip route 192.168.3.0 255.255.255.0 192.168.12.2`
   - On **R2**: `ip route 192.168.1.0 255.255.255.0 192.168.12.1`

3. **Verified connectivity** – `PC1` ping to `PC2` (192.168.3.1):

C:>PING 192.168.3.1

Pinging 192.168.3.1 with 32 bytes of data:
Reply from 192.168.3.1: bytes=32 time=1ms TTL=125
Reply from 192.168.3.1: bytes=32 time=1ms TTL=123
Reply from 192.168.3.1: bytes=32 time=1ms TTL=123
Reply from 192.168.3.1: bytes=32 time=1ms TTL=123

Ping statistics for 192.168.3.1:
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)


### Key takeaways

- **Static routes** manually tell a router how to reach networks not directly connected.
- Syntax: `ip route <destination_network> <subnet_mask> <next_hop_ip>`
- Each router needs a route **back** to the source network – routing is bidirectional.
- TTL starting at 128 (Windows) decreased by 1 for each router hop → TTL=125 indicates 3 hops? (128-3=125) but observed values 125,123 suggest maybe two hops? Still shows successful path.
- Switches at Layer 2 don’t need IP configuration for basic connectivity.

This lab reinforced how static routing connects multiple subnets and why both directions are essential for ping replies.

<img width="1409" height="733" alt="Toplogy Screenshot" src="https://github.com/user-attachments/assets/9162739f-8bd1-41da-a1f1-fbe32ed7cefa" />
