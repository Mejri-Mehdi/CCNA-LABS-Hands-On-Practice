## What I Learned

In this lab, I analyzed an existing OSPF network and then configured **floating static routes** as backup paths in case the primary link between R1 and R2 failed.

### Initial analysis (OSPF already running)

| Question | Answer |
|----------|--------|
| Which dynamic routing protocol is Enterprise A using? | **OSPF** |
| Which route will PC1 use to reach SRV1? | **Direct link R1–R2** (lower metric than the alternative path) |
| Which route will PC1 use to reach remote server 1.1.1.1 over the Internet? | **R1–ISPBR1** (default route or OSPF path) |

- Verified by pinging SRV1 and 1.1.1.1 – both worked via the expected primary paths.

### Floating static route configuration

**On R1:**  
`ip route 172.16.1.0 255.255.255.0 10.0.0.2 150` (administrative distance 150, higher than OSPF’s default 110)

**On R2:**  
`ip route 192.168.1.0 255.255.255.0 10.0.0.1 150`

#### Do the routes enter the routing tables immediately?  
**NO** – because OSPF routes have a lower AD (110), so the static routes with AD 150 are not installed while the primary OSPF path is available.

### Failover test

1. **Shut down the G0/2/0 interface on R1 or R2** (the link between them).
2. **Check routing tables again** – the floating static routes now appear because the OSPF route has been removed.
3. **Ping from PC1 to SRV1** – still succeeds, now using the backup path (via the alternative route, likely through other routers or ISPBR1).

### Key takeaways

- **Floating static route** = a static route with a higher administrative distance (AD) than the primary dynamic route.
- **AD comparison** – OSPF = 110, static = 1 by default, but floating static uses a value >110 (e.g., 150) so it only appears when the primary route disappears.
- **Failover** is automatic – when the primary link goes down, the router installs the backup static route.
- **Verification commands:**  
  - `show ip route` – to see which routes are currently active.  
  - `show ip route 172.16.1.0` – to see the specific route details.  
  - `ping` – to confirm connectivity after failover.

This lab demonstrated how to provide simple backup connectivity without dynamic routing reconvergence delays.

<img width="821" height="696" alt="Topology Screenshot" src="https://github.com/user-attachments/assets/f1a4e8cb-1032-453d-a1d4-848174f478c7" />
