## What I Learned

In this lab, I explored how switches learn MAC addresses and forward frames, starting with empty MAC address tables and empty ARP tables on all PCs.

### Tasks performed

1. **Predicted the outcome** – If `PC1` pings `PC3` (on a different switch), what messages are sent and which devices receive them?  
   - Initial ARP broadcast to find PC3’s MAC (flooded to all ports except the source).  
   - Then ICMP echo request/reply, with switches learning MAC addresses incrementally.

2. **Verified with Simulation Mode** – The event list confirmed ICMP packets traversing `PC1 → SW1 → SW2 → PC3` and back.

3. **Generated traffic to populate MAC tables** – Sent multiple pings between PCs so switches learned the MAC addresses of all devices.

4. **Displayed learned MAC addresses** – Used `show mac address-table` on each switch to see dynamic entries (VLAN, MAC, port).

5. **Cleared dynamic MAC addresses** – Used `clear mac address-table dynamic` on each switch, then verified with `show mac address-table` (table empty).

### Key takeaways

- **Switches learn MAC addresses** by examining the source MAC of incoming frames and associating it with the arrival port.
- **Unknown unicast or broadcast frames** are flooded out all ports except the source until the destination MAC is learned.
- **ARP is essential** for Layer 3 to Layer 2 address resolution – without a pre‑populated ARP cache, pings trigger ARP broadcasts first.
- **Simulation mode** is invaluable for visualizing frame paths and switch learning in real time.
- **Dynamic MAC entries** can be cleared for troubleshooting or security exercises.

This lab deepened my understanding of how Layer 2 switching works, from flooding to forwarding, and how to manage the MAC address table.