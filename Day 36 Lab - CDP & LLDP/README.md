## What I Learned

In this lab, I used **CDP** (Cisco Discovery Protocol) to discover neighboring devices and identify missing IP addresses and interface IDs. I then disabled CDP where not needed (on PC‑connected interfaces and globally) and enabled **LLDP** (Link Layer Discovery Protocol) as a vendor‑neutral alternative.

### Tasks performed

#### 1. Use CDP to discover missing information
- Used `show cdp neighbors` and `show cdp neighbors detail` on routers and switches.
- Identified **missing IP addresses** and **interface IDs** of devices in the network.
- CDP revealed:
  - Neighbor device hostnames
  - Local interface connected to the neighbor
  - Neighbor's interface
  - Capabilities (router, switch, etc.)
  - Platform and IOS version
  - IP address of the neighbor (if configured)
- Labelled missing information in the diagram based on CDP output.

#### 2. Disable CDP on switch interfaces connected to PCs
- On access ports (connected to end hosts), CDP is unnecessary and can be a security risk.
- Command: `interface <int>` → `no cdp enable`
- This disables CDP **only on that interface**, not globally.

#### 3. Disable CDP globally on each network device
- Command (global config): `no cdp run`
- After this, CDP is disabled on **all interfaces** of the device.
- Verified with `show cdp` – output shows CDP is not running.

#### 4. Enable LLDP globally on each network device
- Command (global config): `lldp run`
- This enables LLDP on all interfaces (Tx/Rx) – **but Tx/Rx are disabled by default** on interfaces; they need to be enabled per interface.

#### 5. Enable LLDP Tx/Rx on interfaces connected to other network devices
- By default, LLDP Tx/Rx are disabled on all interfaces.
- For **network‑to‑network interfaces** (router‑to‑router, switch‑to‑switch), enabled both transmit and receive:
  - `interface <int>` → `lldp transmit`
  - `interface <int>` → `lldp receive`
- (For **PC‑connected interfaces**, LLDP can be left disabled or not needed.)
- Verified with `show lldp neighbors` and `show lldp neighbors detail`.

### Key takeaways

- **CDP** is Cisco‑proprietary, enabled by default on Cisco devices – useful for discovery but can be a security risk.
- **Disable CDP** on **access ports** facing end hosts to prevent information leakage.
- **Disable CDP globally** if not needed, or if using a multi‑vendor environment.
- **LLDP** is an IEEE standard (802.1AB) – vendor‑neutral and more interoperable.
- LLDP **must be enabled globally** (`lldp run`) and **per‑interface** (`lldp transmit` / `lldp receive`) – transmit/receive are disabled by default on most Cisco devices.
- Use `show cdp neighbors` and `show lldp neighbors` to verify neighbour information.

### Verification commands

show cdp neighbors
show cdp neighbors detail
show cdp
show lldp neighbors
show lldp neighbors detail
show lldp

This lab reinforced the importance of discovery protocols, their security implications, and the transition from proprietary CDP to standard LLDP.