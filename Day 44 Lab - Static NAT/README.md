## What I Learned

In this lab, I configured **Static NAT** on R1 to allow PCs on the internal network (`172.16.0.0/24`) to access the Internet (`8.8.8.8`). Each PC was mapped to a unique public IP address from the `100.0.0.x/24` range.

### Network overview

| Device | Interface | IP Address | NAT Role |
|--------|-----------|------------|----------|
| PC1 | NIC | 172.16.0.1/24 | Inside local |
| PC2 | NIC | 172.16.0.2/24 | Inside local |
| PC3 | NIC | 172.16.0.3/24 | Inside local |
| R1 G0/1 | 172.16.0.254/24 | Inside interface |
| R1 G0/0 | (Internet IP) | Outside interface |
| Server | – | 8.8.8.8 | Internet destination |

### Tasks performed

#### 1. Initial ping test
- Attempted `ping 8.8.8.8` from PC1 – **failed** (no NAT configured, private IP cannot route to the Internet).

#### 2. Configured Static NAT on R1
- **Defined inside/outside interfaces:**
  - `interface g0/1` → `ip nat inside`
  - `interface g0/0` → `ip nat outside`

- **Mapped each PC to a public IP address:**
  - `ip nat inside source static 172.16.0.1 100.0.0.1`
  - `ip nat inside source static 172.16.0.2 100.0.0.2`
  - `ip nat inside source static 172.16.0.3 100.0.0.3`

#### 3. Tested ping again
- `ping 8.8.8.8` from PC1 – **successful** (R1 translates the source IP to `100.0.0.1` before forwarding).

#### 4. Pinged google.com from each PC
- Since DNS resolution was configured (or using IP `8.8.8.8`), pings worked.
- Checked NAT translations on R1 with `show ip nat translations`:
  - Static entries for PC1, PC2, PC3 remained active.
  - Dynamic entries for the Internet server (8.8.8.8) appeared for each session.

#### 5. Cleared NAT translations
- Command: `clear ip nat translation *`
- **Which entries remained?** The **static NAT entries** (`100.0.0.1` ↔ `172.16.0.1`, etc.) **remained** because they are statically configured. Only the **dynamic entries** (for the 8.8.8.8 server) were cleared.

### Key takeaways

- **Static NAT** provides a fixed one‑to‑one mapping between an inside local IP and an inside global (public) IP – useful for servers that must be reachable from outside.
- **Inside/outside interfaces** must be correctly identified for NAT to work.
- **`ip nat inside source static`** creates permanent mappings.
- **`show ip nat translations`** displays both static and dynamic translations.
- **`clear ip nat translation *`** removes all **dynamic** translations but **does not affect static** entries.

### Verification commands

show ip nat translations
show ip nat statistics
show ip interface brief
ping 8.8.8.8

This lab introduced Static NAT and how it enables private‑to‑public IP translation for Internet access.

<img width="1050" height="580" alt="Topology Screenshot" src="https://github.com/user-attachments/assets/81a86be1-ce36-451d-91b0-f98c3cf17aad" />
