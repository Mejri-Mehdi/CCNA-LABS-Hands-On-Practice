## What I Learned

In this lab, I configured **DHCP (Dynamic Host Configuration Protocol)** pools on R2 to assign IP addresses to clients in two subnets. I then configured R1's interface as a **DHCP client** and set up R1 as a **DHCP relay agent** to forward requests from PC1 (in the 192.168.1.0/24 subnet) to R2.

### Network overview

| Device | Interface | Subnet | Role |
|--------|-----------|--------|------|
| R1 G0/0 | 192.168.1.0/24 | DHCP client (gets IP from POOL1) | Connects to SW1 (PC1) |
| R1 G0/1 | (connected to R2) | – | Link to R2 |
| R2 G0/0 | (connected to R1) | – | Link to R1 |
| R2 G0/1 | 192.168.2.0/24 | DHCP server (POOL2) | Connects to SW2 (PC2) |
| PC1 | 192.168.1.0/24 | DHCP client (via relay) | Connected to SW1 |
| PC2 | 192.168.2.0/24 | DHCP client (direct) | Connected to SW2 |

### Tasks performed

#### 1. Configured DHCP pools on R2

**POOL1** – for `192.168.1.0/24` (reserve .1 to .10):
ip dhcp pool POOL1
network 192.168.1.0 255.255.255.0
default-router 192.168.1.1
dns-server 8.8.8.8
domain-name jeremysitlab.com
ip dhcp excluded-address 192.168.1.1 192.168.1.10

**POOL2** – for `192.168.2.0/24` (reserve .1 to .10):
ip dhcp pool POOL2
network 192.168.2.0 255.255.255.0
default-router 192.168.2.1
dns-server 8.8.8.8
domain-name jeremysitlab.com
ip dhcp excluded-address 192.168.2.1 192.168.2.10

**POOL3** – for `203.0.113.0/30` (reserve .1):
ip dhcp pool POOL3
network 203.0.113.0 255.255.255.252
ip dhcp excluded-address 203.0.113.1

#### 2. Configured R1's G0/0 as a DHCP client
- Command: `interface g0/0` → `ip address dhcp`
- R1's G0/0 received an IP from POOL1 (likely `192.168.1.11` or another address outside the reserved range).

#### 3. Configured R1 as a DHCP relay agent for the `192.168.1.0/24` subnet
- On R1's interface facing PC1 (G0/0):
  - `interface g0/0` → `ip helper-address <R2_IP>`
- This forwards DHCP broadcast requests from PC1 to R2, which then assigns an IP from POOL1.
- The relay agent uses the **source IP** of the interface (R1's G0/0) to determine which pool to use.

#### 4. Requested IP addresses from PC1 and PC2
- On each PC, used CLI to request an IP (e.g., `ipconfig /renew` on Windows or `dhcp` on Packet Tracer's PC).
- PC1 received an IP from POOL1 (via relay).
- PC2 received an IP from POOL2 (directly from R2).

### Key takeaways

- **Excluded addresses** prevent the DHCP server from assigning critical IPs (routers, servers, etc.).
- **`ip helper-address`** forwards DHCP broadcast requests across subnets – essential when the DHCP server is on a different subnet.
- **Relay agent** uses the interface's IP address to determine which pool to assign from (the `network` statement in the pool).
- A router interface can be a **DHCP client** (gets its own IP from a pool) while also **relaying** DHCP requests for other clients.
- **Domain name** and **DNS server** are pushed to clients along with the IP address.

### Verification commands

show ip dhcp binding
show ip dhcp pool
show ip interface brief

This lab demonstrated DHCP server configuration, client addressing, and relay agent functionality in a multi‑subnet network.
