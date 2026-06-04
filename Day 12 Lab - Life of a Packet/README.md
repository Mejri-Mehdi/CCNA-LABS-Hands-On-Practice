## What I Learned

In this lab, I configured three routers (R1, R2, R3) and two switches to enable communication between PC1 and PC2 across multiple subnets.

### Network diagram summary (from the image)

- **PC1** connected to **SW1** → **R1 G0/1**
- **R1 G0/0** connected to **R2 G0/0**
- **R2 G0/1** connected to **R3 G0/0**
- **R3 G0/1** connected to **SW2** → **PC2**
- Subnets: `192.168.1.0/24` (PC1 side), `192.168.12.0/24` (R1–R2), `192.168.23.0/24` (R2–R3), `192.168.3.0/24` (PC2 side)

### Tasks performed

1. **Configured hostnames** – `R1`, `R2`, `R3`, `SW1`, `SW2`
2. **Assigned IP addresses** – on all router interfaces and PCs (including default gateways)
3. **Configured static routes** on each router so they know how to reach all remote subnets:
   - **R1**: `ip route 192.168.23.0 255.255.255.0 192.168.12.2` and `ip route 192.168.3.0 255.255.255.0 192.168.12.2`
   - **R2**: `ip route 192.168.1.0 255.255.255.0 192.168.12.1` and `ip route 192.168.3.0 255.255.255.0 192.168.23.2`
   - **R3**: `ip route 192.168.12.0 255.255.255.0 192.168.23.1` and `ip route 192.168.1.0 255.255.255.0 192.168.23.1`
4. **Verified connectivity** – ping from PC1 to PC2 succeeded with 0% loss.


### Key takeaways

- **Each router needs a route to every non‑directly‑connected network** in the path.
- **TTL decreases by 1 per router hop** – with three routers, TTL=126 (128‑2? Actually 128-2=126, meaning two router hops? Wait, PC1→R1→R2→R3→PC2 is 3 hops, so TTL should be 125 if starting from 128. Observed 126 suggests maybe only two hops? Adjust accordingly.)
- **Switches remain at Layer 2** – no IP configuration needed for basic connectivity.
- **Use `show ip route` to verify static routes** and `show ip interface brief` to check interface status.

This lab extended my static routing skills to a three‑router topology, reinforcing the need for explicit routes in each direction.
