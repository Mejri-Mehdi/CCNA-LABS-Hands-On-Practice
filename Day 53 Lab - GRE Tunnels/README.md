# GRE Tunnel & OSPF over Tunnel Lab – Packet Tracer

## What I Learned

In this lab, I configured a **GRE (Generic Routing Encapsulation) tunnel** between R1 and R2 across a service provider network, then ran **OSPF over the tunnel** to allow PC1 (Office A) and PC2 (Office B) to communicate.

### Network overview

| Device | Interface | IP Address | Network |
|--------|-----------|------------|---------|
| R1 G0/0/0 | 100.0.0.1/30 | Service Provider (SPR1) |
| R1 Tunnel0 | 192.168.1.1/30 | GRE tunnel |
| R2 G0/0/0 | 200.0.0.2/30 | Service Provider (SPR2) |
| R2 Tunnel0 | 192.168.1.2/30 | GRE tunnel |
| PC1 | 10.0.1.100/24 | Office A (via SW1) |
| PC2 | 10.0.2.100/24 | Office B (via SW2) |
| R1 G0/1 | 10.0.1.254/24 | Office A LAN |
| R2 G0/1 | 10.0.2.254/24 | Office B LAN |

- **Service Provider (SPR1/SPR2)** provides connectivity between R1 and R2.
- **GRE tunnel** acts as a virtual point‑to‑point link between R1 and R2.

### Tasks performed

#### 1. Configured GRE tunnel between R1 and R2

**On R1:**

interface tunnel 0
ip address 192.168.1.1 255.255.255.252
tunnel source 100.0.0.1
tunnel destination 200.0.0.2

**On R2:**

interface tunnel 0
ip address 192.168.1.2 255.255.255.252
tunnel source 200.0.0.2
tunnel destination 100.0.0.1

> The tunnel uses the **physical interface IPs** (toward SPR1/SPR2) as source/destination. GRE encapsulates the payload (e.g., OSPF packets) inside an IP header, so the tunnel is treated as a logical link.

#### 2. Configured OSPF on the tunnel interfaces of R1 and R2

**On R1:**

router ospf 1
network 192.168.1.0 0.0.0.3 area 0
network 10.0.1.0 0.0.0.255 area 0

**On R2:**

router ospf 1
network 192.168.1.0 0.0.0.3 area 0
network 10.0.2.0 0.0.0.255 area 0

> OSPF runs over the GRE tunnel, treating it as a direct link. R1 and R2 form an OSPF adjacency over the tunnel interface.

#### 3. Verified connectivity
- **PC1** ping to **PC2** (`10.0.2.100`) succeeded.
- Checked OSPF neighbor adjacency on the tunnel:
  - `show ip ospf neighbor` – R1 and R2 should be **FULL/ -** over Tunnel0.
- Checked routing table on R1/R2:
  - `show ip route ospf` – `10.0.2.0/24` appears via Tunnel0.

### Key takeaways

- **GRE tunnel** encapsulates packets inside an IP header, creating a virtual point‑to‑point link across an IP network (like the Internet or a service provider network).
- **Tunnel source/destination** must be reachable (routable) IP addresses.
- **OSPF over a tunnel** is possible – the tunnel interface acts like a physical link, allowing routing protocols to run over it.
- **OSPF network type** defaults to point‑to‑point on GRE tunnels (no DR election).
- The tunnel IP subnet (`192.168.1.0/30`) is used solely for the tunnel – PCs use their own subnets (`10.0.1.0/24` and `10.0.2.0/24`).
- **Recursive routing** is not an issue here because the tunnel endpoints (physical IPs) are not learned via the tunnel itself.

### Verification commands

show ip interface brief
show ip ospf neighbor
show ip route
show interfaces tunnel 0
ping 10.0.2.100

This lab introduced GRE tunneling and how to run OSPF over a tunnel for private network interconnection.