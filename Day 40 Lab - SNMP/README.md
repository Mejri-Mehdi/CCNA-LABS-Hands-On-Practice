## What I Learned

In this lab, I configured **SNMP (Simple Network Management Protocol)** on R1 with read‑only and read/write community strings. I then used the **MIB Browser** on PC1 to query and modify R1's configuration.

> **Note:** SNMP functionality in Packet Tracer is **very limited** compared to real devices. Some OIDs may not be fully supported.

### Network overview

| Device | Interface | IP Address |
|--------|-----------|------------|
| R1 G0/0 | 192.168.1.254/24 | Connects to SW1 (PC1) |
| PC1 | 192.168.1.1/24 | SNMP manager (MIB Browser) |
| SW1 | – | Layer 2 connectivity |

### Tasks performed

#### 1. Configured SNMP communities on R1
- **Read‑only community** – allows `GET` requests only:
  - Command: `snmp-server community Cisco1 ro`
- **Read/write community** – allows `GET` and `SET` requests:
  - Command: `snmp-server community Cisco2 rw`

#### 2. Used SNMP `GET` messages from PC1 (MIB Browser)
- Opened the **MIB Browser** on PC1 (found under Desktop → MIB Browser).
- Connected to R1's IP (`192.168.1.254`) using the read‑only community (`Cisco1`).
- Queried the following OIDs (Object Identifiers):
  - **System uptime** – `1.3.6.1.2.1.1.3.0` (how long R1 has been running)
  - **System hostname** – `1.3.6.1.2.1.1.5.0` (currently configured hostname)
  - **Number of interfaces** – `1.3.6.1.2.1.2.1.0` (interface count)
  - **Interface list** – `1.3.6.1.2.1.2.2.1.2` (descriptions of each interface)
- Explored other available OIDs to see what additional information could be retrieved (e.g., interface IP addresses, status, etc.).

#### 3. Used SNMP `SET` message to change the hostname of R1
- In the MIB Browser, switched to the read/write community (`Cisco2`).
- Located the `sysName.0` OID (`1.3.6.1.2.1.1.5.0`).
- Performed a `SET` operation to change the value to a new hostname.
- Verified the change by:
  - Doing a `GET` on the same OID.
  - Checking the CLI prompt on R1 (or using `show running-config`).

### Key takeaways

- **SNMP** allows remote monitoring and configuration of network devices.
- **Community strings** act as passwords – `ro` (read‑only) for monitoring, `rw` (read/write) for configuration changes.
- **MIB (Management Information Base)** defines the structure of manageable objects; OIDs identify specific variables.
- **`GET`** retrieves data; **`SET`** modifies it.
- SNMP **should be secured** in production (use version 3 with authentication/encryption) – version 1/2c sends community strings in clear text.
- Packet Tracer supports basic SNMP operations but lacks many advanced features (traps, informs, etc.).

### Verification commands

show snmp
show running-config | include snmp

This lab introduced SNMP as a powerful network management tool, even with Packet Tracer's limitations.