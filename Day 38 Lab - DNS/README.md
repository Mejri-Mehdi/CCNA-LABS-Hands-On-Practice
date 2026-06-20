## What I Learned

In this lab, I configured a **DNS (Domain Name System) server** to provide name‑to‑IP address resolution for multiple clients on the network. This allows devices to use human‑readable names (e.g., `youtube.com`) instead of numeric IP addresses.

### Network overview

- One **DNS Server** (`Server-PT`) with the domain name **`youtube.com`**.
- Multiple **clients** (Server‑PT devices) with IP addresses `1.1.1.1`, `1.2`, `1.3`, etc.
- All devices connected to a switch (`2960-24TT`).

### Tasks performed

1. **Configured the DNS Server**:
   - Set a static IP address on the DNS server.
   - Enabled DNS service: **Services → DNS → On**.
   - Added **A (Address) Records** mapping each client hostname to its IP address:
     - `1.1.1.1` → `1.1.1.1`
     - `1.2` → `1.2`
     - `1.3` → `1.3`
     - ... and so on for all clients.

2. **Configured DNS on each client**:
   - Set each client's **DNS Server** field to the DNS server's IP address.
   - Ensured static IP addresses were assigned to each client.

3. **Tested DNS resolution**:
   - From any client, used `nslookup <hostname>` (if available) or `ping <hostname>` to verify resolution.
   - Example: `ping youtube.com` should resolve to the DNS server's IP address and respond.
   - Also tested resolving other hostnames like `1.2`, `1.3`, etc.

### Key takeaways

- **DNS** translates human‑readable names to IP addresses – critical for internet usability.
- **A Records** map hostnames to IPv4 addresses (AAAA records are for IPv6).
- Each client needs the **DNS server's IP** configured to perform name resolution.
- **Static IPs** are required on servers (and often clients in lab environments) to ensure consistent addressing.
- `nslookup` and `ping` are useful tools to verify DNS functionality.

### Verification commands

ping youtube.com
nslookup youtube.com

This lab introduced DNS configuration and its role in network communication.