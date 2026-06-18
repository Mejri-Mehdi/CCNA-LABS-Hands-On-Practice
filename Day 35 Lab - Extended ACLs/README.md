## What I Learned

In this lab, I configured **extended ACLs** (Access Control Lists) on a Cisco ASA firewall to enforce specific traffic policies between different subnets. Extended ACLs filter traffic based on **source IP, destination IP, protocol, and port numbers** – much more granular than standard ACLs.

### Network diagram summary

| Subnet | Devices |
|--------|---------|
| 172.16.1.0/24 | PC1, PC2 |
| 172.16.2.0/24 | PC3, PC4 |
| 172.16.3.0/24 | PC5, PC6 |
| 10.1.1.0/30 | R1 ↔ ASA link |
| 192.168.1.0/24 | Servers (SRV1-SRVx) |

**R1** connects to the **ASA**, which connects to the internal switch (SW1) with all PCs and servers.

### Traffic policies to enforce (Extended ACLs)

| Policy | Source | Destination | Protocol/Port | Action |
|--------|--------|-------------|---------------|--------|
| 1 | 172.16.1.0/24 | 172.16.2.0/24 | HTTP (80), HTTPS (443) | Permit |
| 2 | 172.16.2.0/24 | 172.16.1.0/24 | ICMP | Permit |
| 3 | 172.16.3.0/24 (PC3 only) | 10.1.1.0/30 | Telnet (23) | Permit |
| 4 | Any | Any | Any | Deny (implicit) |

### Tasks performed

#### 1. Extended numbered ACL on ASA (or router)

**Policy 1 – HTTP/HTTPS from 172.16.1.0/24 to 172.16.2.0/24:**

access-list 100 permit tcp 172.16.1.0 0.0.0.255 172.16.2.0 0.0.0.255 eq 80
access-list 100 permit tcp 172.16.1.0 0.0.0.255 172.16.2.0 0.0.0.255 eq 443

**Policy 2 – ICMP from 172.16.2.0/24 to 172.16.1.0/24:**

access-list 100 permit ip 172.16.2.0 0.0.0.255 172.16.1.0 0.0.0.255

**Implicit deny** – all other traffic is blocked.

#### 2. Applied ACL to interface (ASA or router)
- On ASA (or router interface), applied inbound/outbound:
  - `access-group 100 in interface inside` (or `out` depending on traffic direction)

#### 3. Verification
- Tested each policy with pings and specific traffic (HTTP, HTTPS, Telnet) from source to destination.
- Confirmed that:
  - ✅ 172.16.1.0/24 can HTTP/HTTPS to 172.16.2.0/24
  - ❌ 172.16.1.0/24 cannot ping 172.16.2.0/24 (ICMP not permitted)
  - ✅ 172.16.2.0/24 can ping 172.16.1.0/24
  - ❌ 172.16.2.0/24 cannot HTTP/HTTPS to 172.16.1.0/24 (not permitted)
  - ✅ PC3 can Telnet to 10.1.1.0/30
  - ❌ Other PCs in 172.16.3.0/24 cannot Telnet to 10.1.1.0/30

### Key takeaways

- **Extended ACLs** (100–199) filter on source IP, destination IP, protocol, and port.
- **Wildcard masks** (`0.0.0.255`) define the network range in ACLs.
- ACLs are processed **top‑down** – the first match wins.
- **Implicit deny** blocks everything not explicitly permitted.
- **Placement matters** – ACLs should be applied as close to the source as possible (or on the firewall interface).
- **ASA syntax** uses `access-list` and `access-group` (slightly different from IOS routers, but similar logic).

### Verification commands

show access-lists
show access-list 100
show run access-list
ping <destination_IP>

This lab gave me hands‑on experience with extended ACLs to implement precise security policies in a routed network.

<img width="1370" height="648" alt="Topology Screenshot" src="https://github.com/user-attachments/assets/bdfd35fa-2f44-483c-84c4-05dd788fe89d" />
