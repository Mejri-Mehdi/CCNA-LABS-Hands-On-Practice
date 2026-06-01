## What I Learned

In this lab, I configured router and switch interfaces with proper IP addressing, manually set speed and duplex on device‑to‑device links, added descriptions, and disabled unused ports.

### Tasks performed

1. **Configured hostnames** – `R1`, `SW1`, `SW2` using `hostname` in global config.
2. **Assigned IP addresses** – on `R1` interfaces and on `PC1`, `PC2`, `PC3`, `PC4` (static IPs with default gateways).
3. **Manually set speed and duplex** – on links between networking devices (router‑to‑switch or switch‑to‑switch), not on end‑host connections.
   - Example: `interface g0/1` → `speed 100` → `duplex full`
4. **Added interface descriptions** – `description Link to SW1` for documentation.
5. **Disabled unused interfaces** – `shutdown` on ports with no cable connected.
6. **Tested connectivity** – pings initially failed (100% loss), then after correct configuration succeeded (replies with TTL=128).

### Sample ping output observed

C:>PING 172.16.0.4

Pinging 172.16.0.4 with 32 bytes of data:
Reply from 172.16.0.4: bytes=32 time<1ms TTL=128
Reply from 172.16.0.4: bytes=32 time<1ms TTL=128

### Key takeaways

- **Speed and duplex** should match on both ends of a link – mismatches cause errors and poor performance.
- **Auto‑negotiation** is convenient but sometimes fails; manual configuration ensures predictable operation.
- **Interface descriptions** are critical for large networks – they identify purpose without tracing cables.
- **Disabling unused ports** improves security and reduces broadcast domain noise.
- **`shutdown` / `no shutdown`** controls administrative state – unused ports should remain `shutdown`.

This lab reinforced professional interface hygiene: correct speed/duplex, clear descriptions, and disabled unused ports.