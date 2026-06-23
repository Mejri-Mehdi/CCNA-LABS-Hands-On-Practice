## What I Learned

In this lab, I configured **port security** on switch interfaces to restrict MAC address access, prevent unauthorized devices from connecting, and observe how different violation modes handle security breaches.

### Network overview

| Device | Interface | Connected to |
|--------|-----------|--------------|
| SW1 | F0/1 | PC1 |
| SW1 | F0/2 | PC2 |
| SW1 | F0/3 | PC3 |
| SW1 | G0/1 | R1 G0/1 |
| SW1 | G0/2 | SW2 G0/2 |
| SW2 | G0/1 | SW1 G0/2 |
| SW2 | G0/2 | R1 G0/2 |
| R1 | G0/1 | SW1 G0/1 |
| R1 | G0/2 | SW2 G0/2 |

### Tasks performed

#### 1. Configured port security on SW1 interfaces

**Interfaces:** F0/1, F0/2, F0/3

| Setting | Value |
|---------|-------|
| Violation mode | Shutdown |
| Maximum addresses | 1 |
| Sticky learning | Disabled |
| Aging time | 1 hour |

**Configuration commands (example for F0/1):**

interface f0/1
switchport mode access
switchport port-security
switchport port-security maximum 1
switchport port-security violation shutdown
switchport port-security aging time 60
switchport port-security aging type inactivity

> **Note:** Since sticky learning is disabled, the switch learns the first MAC address seen dynamically. Aging is set to 60 minutes (1 hour).

#### 2. Configured port security on SW2 G0/1

| Setting | Value |
|---------|-------|
| Violation mode | Restrict |
| Maximum addresses | 4 |
| Sticky learning | Enabled |

**Configuration commands:**

interface g0/1
switchport mode access
switchport port-security
switchport port-security maximum 4
switchport port-security violation restrict
switchport port-security mac-address sticky

#### 3. Triggered port security violations

**On SW1 (Shutdown violation):**
- Connected another PC (or changed the connected device) to F0/1, F0/2, or F0/3.
- Since maximum addresses = 1, the new MAC address triggered a violation.
- **Result:** The port went into **error-disabled** state (shut down).
- Observed with `show interfaces status` – port status shows `err-disabled`.
- To recover: `shutdown` followed by `no shutdown` on the interface.

**On SW2 G0/1 (Restrict violation):**
- Connected a fifth device (since max = 4) to G0/1.
- **Result:** The port remained up, but packets from the unauthorized MAC were dropped.
- **Observations:**
  - `show port-security` – violation counter incremented.
  - `show port-security address` – only 4 sticky MAC addresses are allowed.
  - No port shutdown – restrict mode logs the violation but continues forwarding for allowed addresses.

### Key takeaways

- **Port security** prevents unauthorized devices from connecting to switch ports.
- **Violation modes:**
  - **Shutdown** – disables the port (most secure, requires manual recovery).
  - **Restrict** – drops traffic from unauthorized MACs, increments violation counter, sends syslog/SNMP but keeps port up.
  - **Protect** – drops traffic from unauthorized MACs but does not increment counter or log.
- **Maximum addresses** – limits how many MACs can be learned on a port.
- **Sticky learning** – dynamically learned MACs are saved in the running config; prevents relearning after reload.
- **Aging** – removes learned MACs after the configured time (inactivity or absolute).
- **Recovery from shutdown:** `shutdown` + `no shutdown` (or `errdisable recovery` globally).

### Verification commands

show port-security
show port-security address
show port-security interface f0/1
show interfaces status
show running-config | include port-security

This lab demonstrated practical switch port security configuration and the behaviour of different violation modes in response to MAC address violations.

<img width="914" height="698" alt="Topology Screenshot" src="https://github.com/user-attachments/assets/cd739e95-0fb4-4d00-9ced-238b857ad9a8" />
