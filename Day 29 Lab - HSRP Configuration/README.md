## What I Learned

In this lab, I configured **HSRPv2** on R1 and R2 to provide first‑hop redundancy for PC1 and PC2. I tested failover by shutting down the active router and verified that the standby router took over.

### Tasks performed

1. **Initial ping test** – from PC1 and PC2 to `8.8.8.8` (external server).  
   - Noted what the default gateway was configured as before HSRP (likely a single router's IP, e.g., R1's interface IP).

2. **Configured HSRPv2 on R1 and R2**:
   - On the LAN interface (facing PCs): `standby version 2`
   - Assigned a **virtual IP (VIP)** – e.g., `standby 1 ip 10.0.0.254`
   - **Raised R1's priority** above default (default = 100) – e.g., `standby 1 priority 150`
   - (Or lowered R2's priority below default – e.g., `standby 1 priority 90`)
   - **Enabled preemption** on R1: `standby 1 preempt`
   - (Also enabled preemption on R2 if desired, with a lower priority)

3. **Configured VIP as default gateway on PCs** – changed PC1 and PC2's default gateway to the **virtual IP** (e.g., `10.0.0.254`).
   - Tested ping to `8.8.8.8` – it should succeed.
   - Checked **PCs' ARP tables** (`arp -a`) – the VIP is mapped to the **virtual MAC address** (e.g., `0000.0c9f.f001` for HSRP group 1).

4. **Failover test – turned off R1** (after saving config with `write memory`):
   - Restarted R1.
   - Pinged from PC1 to `8.8.8.8` again – traffic still flows.
   - R2 should become the **active** router (taking over the VIP and virtual MAC).
   - Verified with `show standby brief` on R2 – state should be `Active`.

5. **Restored R1** – turned R1 back on:
   - Since preemption was enabled, R1 (with higher priority) should **reclaim** the active role automatically.
   - Verified with `show standby brief` on R1 – state should be `Active`.
   - Ping from PC1 to `8.8.8.8` still works – no downtime.

### Key takeaways

- **HSRP** provides gateway redundancy – multiple routers share a virtual IP and virtual MAC.
- **Priority** determines the active router (higher is better, default = 100).
- **Preemption** allows a higher‑priority router to take over when it comes back online.
- **Virtual MAC** format: `0000.0c07.acXX` (XX = HSRP group number in hex) for HSRPv1; HSRPv2 uses `0000.0c9f.fXXX`.
- **ARP table** on hosts maps the VIP to the virtual MAC, ensuring traffic always goes to the active router.
- **`show standby brief`** – quick view of HSRP state, priority, and role.

### Verification commands

show standby brief
show standby
show ip arp

This lab demonstrated how HSRP provides seamless gateway failover and load‑sharing (if multiple groups are configured).

<img width="890" height="707" alt="Topology Screenshot" src="https://github.com/user-attachments/assets/10eb3d7c-b3ba-4cce-96b0-e51149987d45" />

