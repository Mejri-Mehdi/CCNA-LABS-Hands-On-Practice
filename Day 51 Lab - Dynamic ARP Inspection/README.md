## What I Learned

In this lab, I configured **R1 as a DHCP server**, then implemented **DHCP Snooping** and **Dynamic ARP Inspection (DAI)** on SW1 and SW2 to protect the network against rogue DHCP servers and ARP spoofing attacks.

### Network overview

| Device | Interface | IP Address | Role |
|--------|-----------|------------|------|
| R1 G0/1 | 192.168.1.254/24 | DHCP server, default gateway |
| SW1 | – | Layer 2 switch (with DHCP snooping + DAI) |
| SW2 | – | Layer 2 switch (with DHCP snooping + DAI) |
| PC(s) | – | DHCP clients |

### Tasks performed

#### 1. Configured R1 as a DHCP server
- **Excluded addresses** (reserved for static devices):
  - `ip dhcp excluded-address 192.168.1.1 192.168.1.9`
- **Created DHCP pool:**
  - `ip dhcp pool LAN_POOL`
  - `network 192.168.1.0 255.255.255.0`
  - `default-router 192.168.1.254`
  - `dns-server 8.8.8.8` (optional)

#### 2. Configured DHCP Snooping on SW1 and SW2
- **Enabled globally:**
  - `ip dhcp snooping`
- **Enabled on specific VLANs:**
  - `ip dhcp snooping vlan 1` (or whichever VLAN is used)
- **Set trusted ports** (toward R1 and between switches):
  - `interface <int>` → `ip dhcp snooping trust`
- **Untrusted ports** (toward PCs) remain untrusted by default – they will only accept DHCP replies from trusted ports.
- **Verified** with `show ip dhcp snooping`.

#### 3. Configured DAI (Dynamic ARP Inspection) on SW1 and SW2
- **Enabled globally:**
  - `ip arp inspection vlan 1` (or whichever VLAN is used)
- **Enabled all additional validation checks:**
  - `ip arp inspection validate src-mac dst-mac ip`
    - `src-mac` – validates source MAC in ARP packet matches Ethernet header
    - `dst-mac` – validates destination MAC (for ARP replies)
    - `ip` – validates IP address consistency
- **Trusted ports** connected to routers or switches:
  - `interface <int>` → `ip arp inspection trust`
- **Untrusted ports** (toward PCs) will have ARP packets inspected and dropped if invalid.
- **Verified** with `show ip arp inspection`.

### Key takeaways

- **DHCP Snooping** prevents rogue DHCP servers by only allowing DHCP replies on trusted ports.
- **DAI** prevents ARP spoofing/poisoning attacks by validating ARP packets against the DHCP snooping database.
- **Trusted ports** should be only those connected to **authorized devices** (routers, other switches).
- **Untrusted ports** (end hosts) have all DHCP and ARP traffic inspected for anomalies.
- **Additional validation checks** (`src-mac`, `dst-mac`, `ip`) add extra security by verifying packet contents at multiple layers.
- **Both features** rely on the DHCP snooping binding table to track valid IP‑to‑MAC mappings.

### Verification commands

show ip dhcp snooping
show ip dhcp snooping binding
show ip arp inspection
show ip arp inspection interfaces
show ip arp inspection statistics

This lab introduced critical Layer 2 security features to protect against DHCP and ARP‑based attacks.

<img width="1003" height="677" alt="Topology Screenshot" src="https://github.com/user-attachments/assets/13ee119b-e2b2-4c86-b364-c0cca382271a" />
