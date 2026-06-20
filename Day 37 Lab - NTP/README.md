## What I Learned

In this lab, I configured **NTP** (Network Time Protocol) to synchronize time across routers. Routing (default route on R1, OSPF with `network 0.0.0.0 255.255.255.255 area 0` on all) was **pre‑configured**.

### Tasks performed

1. **Set software clocks** on R1, R2, R3 to `12:00:00 Dec 30 2020 UTC` – used `clock set 12:00:00 Dec 30 2020`.

2. **Configured time zones** to match my local time – `clock timezone <zone> <offset>` (e.g., `clock timezone CET 1`).

3. **Configured R1 as NTP client** to synchronize to external NTP server `1.1.1.1`:
   - Command: `ntp server 1.1.1.1`
   - Checked stratum with `show ntp status`:
     - `1.1.1.1` is **stratum 1** (primary reference).
     - R1 becomes **stratum 2** (one hop away).

4. **Configured R1 as a stratum 8 NTP master** (for internal sync):
   - Command: `ntp master 8`
   - This overrides the external sync – R1 now advertises itself as stratum 8.

5. **Synchronized R2 and R3 to R1 with authentication**:
   - On R1 (server):  
     `ntp authentication-key 1 md5 <password>`  
     `ntp trusted-key 1`  
   - On R2 and R3 (clients):  
     `ntp authentication-key 1 md5 <password>`  
     `ntp trusted-key 1`  
     `ntp server <R1_IP> key 1` (using R1's physical interface IP, since `ntp source` is not available in Packet Tracer)

6. **Configured NTP to update hardware calendars**:
   - Command: `ntp update-calendar` (on each router) – though Packet Tracer doesn't display the calendar, the command is applied.

### Key takeaways

- **Stratum** indicates distance from a reference clock (lower = more accurate). Stratum 1 = primary reference, stratum 2 = client of stratum 1, etc.
- **NTP authentication** prevents rogue time sources – uses a shared key (MD5).
- **NTP master** makes a router act as a time source for others; stratum can be manually set.
- **`ntp source`** is not supported in Packet Tracer, so clients must point to the specific IP of the server's interface.
- **`ntp update-calendar`** syncs the hardware clock (RTC) to the NTP time – ensures time persists after reloads.

### Verification commands

show ntp status
show ntp associations
show clock detail

This lab demonstrated how to set up hierarchical time synchronisation with authentication in a routed network.