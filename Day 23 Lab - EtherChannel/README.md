## What I Learned

In this lab, I configured **EtherChannel** (link aggregation) between access and distribution switches using different protocols – LACP, PAgP, and static. I also configured routing and modified the load‑balancing method.

### Topology summary

- **ASW1** and **DSW1** – Layer 2 EtherChannel using **LACP** (trunk mode)
- **ASW2** and **DSW2** – Layer 2 EtherChannel using **PAgP** (trunk mode)
- **ASW1** and **DSW2** – Layer 3 EtherChannel using **static** (no negotiation)
- **SRV1** in `172.16.1.0/24` – reachable from PCs via routing

End host and SVI IP addresses were **pre‑configured**.

### Tasks performed

1. **LACP EtherChannel (ASW1 ↔ DSW1)**  
   - Created port‑channel interface: `interface port-channel 1`  
   - Set mode `trunk` on the port‑channel  
   - On physical interfaces: `channel-group 1 mode active` (LACP active)  
   - Verified with `show etherchannel summary`

2. **PAgP EtherChannel (ASW2 ↔ DSW2)**  
   - Port‑channel interface: `interface port-channel 2`  
   - Set mode `trunk`  
   - Physical interfaces: `channel-group 2 mode desirable` (PAgP)  
   - Verified with `show etherchannel summary`

3. **Static Layer 3 EtherChannel (ASW1 ↔ DSW2)**  
   - On ASW1 and DSW2, removed switchport mode (`no switchport`) on physical interfaces  
   - Created port‑channel: `interface port-channel 3`  
   - Assigned IP addresses to the port‑channel (Layer 3)  
   - Physical interfaces: `channel-group 3 mode on` (static)  
   - Enabled routing with `ip routing` on both switches

4. **Configured routes for PC‑to‑SRV1 connectivity**  
   - Added static routes or enabled dynamic routing (as per topology) so PCs in different subnets could reach SRV1 (`172.16.1.x`)

5. **Default EtherChannel load‑balancing method**  
   - On Cisco switches, the default is **src‑mac** (source MAC address) for Layer 2 EtherChannel, and **src‑dst‑ip** for Layer 3 port‑channels (depending on platform).  
   - Verified with `show etherchannel load-balance`

6. **Changed load‑balancing to source & destination IP addresses**  
   - Global configuration: `port-channel load-balance src-dst-ip`  
   - This hashes on both source and destination IP – better distribution for IP traffic.

### Key takeaways

- **LACP (active/passive)** and **PAgP (desirable/auto)** negotiate EtherChannel; **static mode (`on`)** requires manual configuration on both ends.
- **Layer 2 EtherChannel** carries multiple VLANs (trunk), while **Layer 3 EtherChannel** is a routed link with an IP address.
- `show etherchannel summary` quickly shows which port‑channels are up (flags: RU, P, etc.).
- Load‑balancing method affects traffic distribution across member links – choose based on traffic patterns (e.g., src‑dst‑ip for IP networks).
- After changing load‑balancing, no interface reset is needed – it takes effect immediately.

This lab gave me hands‑on experience with link aggregation, different negotiation protocols, and fine‑tuning EtherChannel performance.