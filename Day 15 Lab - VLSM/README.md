## What I Learned

In this lab, I took a single `/24` network (`192.168.5.0/24`) and subnetted it to support **four LANs** and a **point‑to‑point WAN link** between R1 and R2. Then I configured static routes so all PCs could ping each other.

### Network requirements

| LAN | Hosts needed | Subnet mask (smallest fit) |
|-----|--------------|----------------------------|
| LAN1 | 45 hosts | `/26` (62 usable) |
| LAN2 | 64 hosts | `/25` (126 usable) |
| LAN3 | 14 hosts | `/28` (14 usable) |
| LAN4 | 9 hosts  | `/28` (14 usable) |
| P2P link | 2 hosts | `/30` (2 usable) |

### Subnet plan (from `192.168.5.0/24`)

| Subnet | Network address | Mask | Usable range | Broadcast |
|--------|----------------|------|--------------|------------|
| LAN2 (largest) | 192.168.5.0 | /25 | 192.168.5.1 – 192.168.5.126 | 192.168.5.127 |
| LAN1 | 192.168.5.128 | /26 | 192.168.5.129 – 192.168.5.190 | 192.168.5.191 |
| LAN3 | 192.168.5.192 | /28 | 192.168.5.193 – 192.168.5.206 | 192.168.5.207 |
| LAN4 | 192.168.5.208 | /28 | 192.168.5.209 – 192.168.5.222 | 192.168.5.223 |
| P2P (R1–R2) | 192.168.5.224 | /30 | 192.168.5.225 – 192.168.5.226 | 192.168.5.227 |

> **Note:** LAN2 needs 64 hosts, so `/25` is the smallest mask (126 usable).  
> LAN1 needs 45 hosts → `/26` (62 usable).  
> LAN3 and LAN4 each need 14 hosts → `/28` (14 usable exactly, but careful: 14 usable fits; `/28` gives 16 addresses, 14 usable).  
> Point‑to‑point uses `/30` (2 usable).

### IP assignment rule

- **First usable IP** → PC in that LAN  
- **Last usable IP** → router interface in that LAN  

**Example for LAN1 (192.168.5.128/26):**  
- PC1 gets `192.168.5.129`  
- R1 G0/0 gets `192.168.5.190`

### Static routes configured

- **On R1** (to reach LAN2, LAN3, LAN4):  
  `ip route 192.168.5.0 255.255.255.128 192.168.5.226` (via P2P link to R2)  
  `ip route 192.168.5.192 255.255.255.240 192.168.5.226`  
  `ip route 192.168.5.208 255.255.255.240 192.168.5.226`

- **On R2** (to reach LAN1):  
  `ip route 192.168.5.128 255.255.255.192 192.168.5.225` (via P2P link to R1)

### Verification

All PCs can ping each other across all four LANs.

### Key takeaways

- **Subnetting from a single /24** requires planning the largest subnet first (LAN2 = 64 hosts → /25).
- **Point‑to‑point links** need only a `/30` (or `/31` if supported) to conserve address space.
- **First/last address convention** (PC first, router last) is a common design practice.
- **Static routes** must point to the **next‑hop IP** on the connected link (the other router’s P2P interface).
- **VLSM** (Variable Length Subnet Mask) allows efficient use of address space by assigning different mask sizes to different LANs.

This lab strengthened my ability to design, subnet, and route a small multi‑LAN network from scratch.

<img width="1031" height="674" alt="Topology Screenshot" src="https://github.com/user-attachments/assets/fa92b626-3abd-43a1-8a40-3fd3bf132ee0" />
