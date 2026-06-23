## What I Learned

In this lab, I configured **QoS (Quality of Service)** on R1 to classify and mark different types of traffic with specific **DSCP (Differentiated Services Code Point)** values and allocate minimum bandwidth guarantees.

### Network overview

| Device | Interface | IP Address |
|--------|-----------|------------|
| PC1 | NIC | 192.168.0.1/24 |
| R1 G0/0/1 | 192.168.0.1/24 | Inside interface |
| R1 G0/0/0 | 172.16.0.1/30 | Outside interface (to R2) |
| R2 G0/0/0 | 172.16.0.2/30 | – |
| Server (jeremysitlab.com) | – | Reachable via R2 |

### Tasks performed

#### 1. Mark HTTPS traffic as AF31 with 10% priority bandwidth
- Created class-map to match HTTPS (TCP port 443):
  class-map match-all HTTPS
  match protocol https
- (Alternative: `match access-group` with ACL for port 443)
- In policy-map, assigned `priority percent 10` (LLQ) and set DSCP AF31 (`set dscp af31`).

#### 2. Mark HTTP traffic as AF32 with 10% bandwidth
- Created class-map to match HTTP (TCP port 80):
  class-map match-all HTTP
  match protocol http
- In policy-map, assigned `bandwidth percent 10` and set DSCP AF32 (`set dscp af32`).

#### 3. Mark ICMP traffic as CS2 with 5% bandwidth
- Created class-map to match ICMP:
  class-map match-all ICMP
  match protocol icmp
- In policy-map, assigned `bandwidth percent 5` and set DSCP CS2 (`set dscp cs2`).

#### 4. Applied policy-map outbound on interface G0/0/0
- Command: `service-policy output <policy_name>`

#### 5. Used Simulation Mode to verify DSCP markings
- **Ping from PC1 to jeremysitlab.com**:
- Traffic marked as **CS2** (DSCP value 16).
- **HTTP access from PC1 to jeremysitlab.com**:
- Traffic marked as **AF32** (DSCP value 28).
- **HTTPS access from PC1 to jeremysitlab.com**:
- Traffic marked as **AF31** (DSCP value 26).

### Key takeaways

- **DSCP (Differentiated Services Code Point)** marks packets for QoS treatment – values are in the ToS/IP Precedence field of the IP header.
- **AF (Assured Forwarding)** classes provide different drop probabilities – AF31, AF32, etc.
- **CS (Class Selector)** values correspond to IP Precedence values (CS2 = 16).
- **Priority queue (LLQ)** provides strict low‑latency treatment for real‑time traffic (e.g., VoIP, HTTPS).
- **Bandwidth** commands allocate guaranteed minimum bandwidth percentages for specific traffic classes.
- **Simulation Mode** in Packet Tracer allows you to view DSCP values in the IP header of captured packets.

### Verification commands

show policy-map interface g0/0/0
show class-map
show policy-map

This lab introduced QoS concepts and how to classify, mark, and prioritize traffic in a router.

<img width="928" height="637" alt="Topology Screenshot" src="https://github.com/user-attachments/assets/fa8b8bd2-4e5e-4883-964a-5c2b52fc9c70" />
