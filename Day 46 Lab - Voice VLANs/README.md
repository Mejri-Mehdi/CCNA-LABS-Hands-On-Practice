## What I Learned

In this lab, I worked with a network that had **pre‑configured telephony settings** on R1 (not relevant to CCNA). I configured switch ports in the correct VLANs, set up **router‑on‑a‑stick (ROAS)** for inter‑VLAN routing, and used Simulation Mode to observe VLAN tagging for both data and voice traffic.

### Network overview

| VLAN | Name | Subnet | Devices |
|------|------|--------|---------|
| 10 | Data | 192.168.10.0/24 | PC1, PC2 |
| 20 | Voice | 192.168.20.0/24 | PH1, PH2 (IP Phones) |

| Device | IP Address | VLAN | Port |
|--------|------------|------|------|
| PC1 | 192.168.10.11/24 | VLAN 10 | SW1 G1/0/2 |
| PC2 | 192.168.10.12/24 | VLAN 10 | SW1 G1/0/3 |
| PH1 | 192.168.20.11/24 | VLAN 20 | SW1 G1/0/1 (?) |
| PH2 | 192.168.20.12/24 | VLAN 20 | SW1 G1/0/4 (?) |
| R1 F0/0 | 192.168.10.1/24 (subinterface) | – | ROAS |
| R1 F0/0.20 | 192.168.20.1/24 (subinterface) | – | ROAS |

### Tasks performed

#### 1. Configured SW1 interfaces in the appropriate VLANs
- **Access ports for PCs** (VLAN 10 – Data):
  - `interface g1/0/2` → `switchport mode access` → `switchport access vlan 10`
  - `interface g1/0/3` → `switchport mode access` → `switchport access vlan 10`
- **Access ports for IP Phones** (VLAN 20 – Voice):
  - `interface g1/0/1` → `switchport mode access` → `switchport access vlan 20`
  - `interface g1/0/4` → `switchport mode access` → `switchport access vlan 20`
- (Alternatively, if PCs connect through phones, used `switchport voice vlan 20` for the voice VLAN and access VLAN 10 for data.)

#### 2. Configured ROAS for connection between SW1 and R1
- On **SW1** (port to R1): configured as a **trunk** to carry both VLANs.
- On **R1 F0/0**: created subinterfaces:
  - `interface f0/0.10` → `encapsulation dot1q 10` → `ip address 192.168.10.1 255.255.255.0`
  - `interface f0/0.20` → `encapsulation dot1q 20` → `ip address 192.168.20.1 255.255.255.0`
- Ensured the main interface `f0/0` was **up** (`no shutdown`).

#### 3. Pinged PC2 from PC1 in Simulation Mode
- Sent an ICMP echo request from PC1 to PC2 (both in VLAN 10).
- **Observation:** The traffic was **NOT tagged** with a VLAN ID on the access ports (they strip tags). However, when crossing the trunk between SW1 and R1, the frame **was tagged** with VLAN 10's tag (since it needs to be routed).

#### 4. Called PH1 from PH2 in Simulation Mode
- Sent a call from PH1 to PH2 (both in VLAN 20).
- **Observation:** The voice traffic was **tagged with VLAN 20** when traversing the trunk (just like the data traffic). The phones used VoIP protocols (e.g., SCCP or SIP) and RTP for audio.

### Key takeaways

- **VLANs** separate broadcast domains – data and voice should be in different VLANs for QoS and security.
- **ROAS** uses subinterfaces with 802.1Q encapsulation to route between VLANs with a single physical link.
- **Access ports** remove VLAN tags; **trunk ports** carry tagged frames.
- **Voice VLANs** are typically configured with `switchport voice vlan` to separate voice and data traffic on the same switch port (if a PC is connected through the phone).
- **Simulation Mode** confirms that trunk links carry VLAN tags, while access ports strip them.

### Verification commands

show vlan brief
show interfaces trunk
show ip interface brief
show running-config | include dot1q

This lab demonstrated how to separate data and voice traffic using VLANs and route between them with ROAS.