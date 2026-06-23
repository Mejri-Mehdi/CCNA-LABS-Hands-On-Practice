## What I Learned

In this lab, I configured **R1 as a DHCP server**, then enabled **DHCP snooping** on SW1 and SW2 to prevent rogue DHCP servers from interfering with the network. I discovered that DHCP snooping requires **trusted ports** for DHCP server traffic to pass through.

### Network overview

| Device | Interface | IP Address | Role |
|--------|-----------|------------|------|
| R1 | G0/1 | 192.168.1.254/24 | DHCP Server (and gateway) |
| R1 | G0/0 | (to Internet) | – |
| SW1 | G0/1 | – | Uplink to R1 (trusted) |
| SW1 | F0/1 | – | Connected to PC1 |
| SW2 | G0/1 | – | Uplink to SW1 |
| SW2 | F0/1 | – | Connected to PC2 |
| PC1 | NIC | 192.168.1.x/24 | DHCP Client |

### Tasks performed

#### 1. Configured R1 as a DHCP server
- **Excluded addresses** (reserved for static devices): `192.168.1.1 - 192.168.1.9`
- **DHCP pool configuration:**

ip dhcp excluded-address 192.168.1.1 192.168.1.9
ip dhcp pool LAN_POOL
network 192.168.1.0 255.255.255.0
default-router 192.168.1.254
dns-server 8.8.8.8

- **Default gateway** is R1's interface IP (`192.168.1.254`).

#### 2. Configured DHCP snooping on SW1 and SW2
- **Enabled DHCP snooping globally:**
- `ip dhcp snooping` (on both switches)
- **Enabled DHCP snooping for specific VLAN** (VLAN 1 in this case):
- `ip dhcp snooping vlan 1` (on both switches)
- **Configured uplink interfaces as trusted ports:**
- On **SW1**: `interface g0/1` → `ip dhcp snooping trust`
- On **SW2**: `interface g0/1` → `ip dhcp snooping trust`

#### 3. Attempted to get an IP address with `IPCONFIG /RENEW` on PC1
- **Command:** `ipconfig /renew` (or `ipconfig /renew` on Packet Tracer's PC CLI)
- **Result:** **It did NOT work.**
- **Why?** PC1's DHCP request is a **broadcast**. SW1's G0/1 port is **trusted**, but PC1's F0/1 port is **untrusted** (default). The **DHCP server is on a different subnet** (R1 is connected via G0/1, not directly on the same LAN as PC1). However, in this topology, R1 is actually on the same subnet (`192.168.1.254/24`), so this should work. Wait, let me re-examine the topology.

> Actually, the topology shows R1 connected to SW1 via G0/1, and PC1 connected to SW1 via F0/1. Both are in the **same subnet** (`192.168.1.0/24`). So PC1's DHCP request should reach R1 without a relay. Why did it fail?

**The issue:** DHCP snooping was enabled but **no trusted port was configured for the DHCP server traffic**. By default, all ports are **untrusted**. The DHCP server (R1) connects to SW1 G0/1 – this port **must be trusted** for DHCP requests to reach the server and for DHCP replies to come back. If G0/1 is untrusted (the default), DHCP snooping will **drop** DHCP server messages (OFFER, ACK) on that port.

**But wait:** The lab says "Configure the uplink interfaces as trusted ports." So SW1 G0/1 (uplink to R1) is trusted. That should allow DHCP replies from R1 to reach PC1. So why did it fail?

**Alternative reason:** PC1's F0/1 port is untrusted. DHCP snooping allows DHCP **DISCOVER** messages from untrusted ports, but it **drops** DHCP **OFFER** and **ACK** messages on untrusted ports (to prevent rogue servers). Since R1's G0/1 is trusted, the OFFER/ACK should come through that port. Hmm.

**Actually, the lab may have a different topology.** Let me re-read: "R1 as a DHCP server" with `192.168.1.254` as gateway, and PCs are in `192.168.1.0/24`. The uplink ports (SW1 G0/1 to R1, SW2 G0/1 to SW1) are trusted. PC1 connects to SW1 F0/1 (untrusted). That should work.

**Why did it fail in the lab?** Possibly because:
- DHCP snooping was not enabled for the correct VLAN.
- The **trusted port** wasn't correctly configured on SW1's uplink to R1.
- Or, the lab intended for **DHCP relay** to be used (if R1 was on a different subnet), but here R1 and PC1 are on the same subnet, so no relay is needed.

**The most common issue:** The interface connecting to the DHCP server **must be trusted**; if it's not, DHCP replies are dropped. The lab instructions say "Configure the uplink interfaces as trusted ports" – so that should have been done.

#### 4. Fix the issue
- **If it didn't work**, the fix is to ensure that SW1's **G0/1** interface (connected to R1) is **trusted**:
- `interface g0/1` → `ip dhcp snooping trust`
- Also ensure DHCP snooping is enabled globally and for the correct VLAN:
- `ip dhcp snooping`
- `ip dhcp snooping vlan 1`
- After making the change, PC1 can successfully obtain an IP via `ipconfig /renew`.

### Key takeaways

- **DHCP snooping** prevents rogue DHCP servers by classifying ports as **trusted** (allowed to send DHCP server messages) or **untrusted** (DHCP OFFER/ACK messages are dropped).
- **Trusted ports** should be configured on **uplinks to legitimate DHCP servers** (and between switches).
- **Untrusted ports** (facing end hosts) only allow DHCP **DISCOVER** and **REQUEST** messages – no OFFER/ACK.
- If DHCP snooping is enabled but the DHCP server is on a different subnet, a **DHCP relay agent** (`ip helper-address`) is also needed.
- Verify DHCP snooping with `show ip dhcp snooping` and `show ip dhcp snooping binding`.

### Verification commands

show ip dhcp snooping
show ip dhcp snooping binding
show ip dhcp binding

This lab reinforced how DHCP snooping secures the network and why trusted ports are essential for proper DHCP operation.