## What I Learned

In this lab, I used **Packet Tracer’s Simulation Mode** to analyze network traffic at different OSI layers.

### Tasks performed

1. **Analyzed traffic in Simulation Mode** – observed packets moving between devices (PC1, SW2, SW1, R1, SRV1).  
   Identified which **OSI layers** were involved (e.g., Layer 2 Ethernet frames, Layer 3 IP packets, Layer 4 TCP/UDP, Layer 7 DHCP).

2. **Released and renewed PC1’s IP address** – generated **Layer 7 (Application layer)** DHCP traffic.  
   - `ipconfig/release` → PC1 sends DHCP release to server.  
   - `ipconfig/renew` → PC1 broadcasts DHCP discover, receives offer, request, and ACK.  
   - Observed these DORA steps in the **Event List** (DHCP protocol visible).

### Key takeaways

- **Simulation Mode** lets you step through packets and see which OSI layers each protocol uses.
- **DHCP is a Layer 7 application** that uses UDP (Layer 4) and IP (Layer 3) to dynamically assign IP addresses.
- The **Event List** shows the path packets take (PC1 → SW2 → SW1 → R1 → SRV1, etc.) and timestamps for analysis.
- Releasing/renewing an IP is a quick way to generate real application‑layer traffic for study.

This lab reinforced how the OSI model applies in practice, especially the difference between lower‑layer switching and upper‑layer protocols like DHCP.