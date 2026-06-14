## What I Learned

In this lab, I configured EIGRP on multiple routers, set up loopback interfaces, and then enabled **unequal‑cost load balancing** on R1 for traffic to `192.168.4.0/24`.

### Tasks performed

1. **Basic configuration** – hostnames, IP addresses, and router interfaces enabled (`no shutdown`).

2. **Loopback interfaces** – created as `/32` addresses (e.g., `1.1.1.1/32` on R1, `2.2.2.2/32` on R2, etc.). Used for router IDs and testing.

3. **EIGRP configuration** on each router:
   - `router eigrp 100` (or another AS number)
   - `no auto-summary` (disables automatic summarisation at classful boundaries)
   - `network` statements to include all required interfaces (including loopbacks)
   - **Passive interfaces** configured where no EIGRP neighbours exist (e.g., loopbacks, LAN-facing interfaces) – `passive-interface <interface>`.

4. **Unequal-cost load balancing on R1** to `192.168.4.0/24`:
   - EIGRP by default does **equal‑cost** load balancing (maximum 4 paths, or 16 with `maximum-paths`).
   - Unequal‑cost uses the `variance` command: `variance <multiplier>`.
   - The multiplier determines which **feasible successors** are used (routes with a metric less than `best_metric * variance`).
   - Also needed `maximum-paths` if more than 4 unequal paths exist.
   - Verified with `show ip route 192.168.4.0` – multiple next‑hop IPs appear with different metrics.
   - Tested load balancing with extended pings or traffic generators.

### Key EIGRP concepts reinforced

- **Auto‑summary** is disabled in modern networks to avoid unintended route aggregation.
- **Passive interfaces** prevent unnecessary hello messages and adjacency formation, improving security and efficiency.
- **Feasible successor** – a backup route that satisfies the feasibility condition (reported distance < feasible distance).
- **Variance** – enables using feasible successors with higher metrics, creating unequal‑cost load balancing.
- **Traffic distribution** is proportional to the metric (lower metric gets more traffic).

### Verification commands
show ip protocols
show ip eigrp interfaces
show ip eigrp topology
show ip route eigrp
show ip route 192.168.4.0

This lab deepened my understanding of EIGRP’s advanced metric‑based load balancing.

<img width="1003" height="720" alt="Topology Screenshot" src="https://github.com/user-attachments/assets/68cadaf1-4b6d-47b8-bc98-0e5324a4809d" />
