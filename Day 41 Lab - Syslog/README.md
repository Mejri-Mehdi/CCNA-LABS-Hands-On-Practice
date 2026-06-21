## What I Learned

In this lab, I configured **syslog** (system logging) on R1 to capture and display log messages via multiple outputs: console, VTY lines, buffer, and a remote syslog server.

### Network overview

| Device | Interface | IP Address | Role |
|--------|-----------|------------|------|
| R1 G0/0 | 192.168.1.254/24 | Router (connected to SW1/PC1) |
| R1 G0/1 | – | Unused (enabled later) |
| PC1 | 192.168.1.12/24 | Telnet client |
| PC2 | 192.168.1.12/24 | Console access (via PC2) |
| SRV1 | 192.168.1.100/24 | Syslog server |

R1 credentials: `username: jeremy`, `password: ccna`, `enable password: ccna`

### Tasks performed

#### 1. Console connection from PC2 to R1
- Connected to R1's console port (via PC2's Terminal).
- **Shut down G0/0**: `interface g0/0` → `shutdown`
- Observed syslog message on the console: `%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to administratively down`
- **Re‑enabled the interface**: `no shutdown`
- **Identified severity level** – the syslog message showed severity `5` (NOTIFICATIONS) for link state changes.
- **Enabled timestamps** for logging messages: `service timestamps log datetime` (or `log datetime msec` for more precision).

#### 2. Telnet from PC1 to R1 (via G0/0)
- From PC1, telnet to R1's G0/0 IP (`192.168.1.254`).
- **Enabled the unused G0/1 interface**: `interface g0/1` → `no shutdown`
- **Why no syslog message appeared?** – Telnet sessions do **not** display syslog messages by default because logging to VTY lines must be explicitly enabled (or is on by default depending on IOS). In Packet Tracer, `logging monitor` is **not available** but it's **enabled by default** – so messages should appear, but the lab may expect that they don't because VTY sessions don't receive logging by default.
- **Enabled logging to VTY lines** – attempted `logging monitor` (but not supported in PT), but noted that logging to VTY is enabled by default, or that `terminal monitor` can be used on the VTY session to receive log messages.

#### 3. Enabled logging to the buffer
- Command: `logging buffered 8192` (configures buffer size to 8192 bytes).
- Messages are now stored in RAM; view with `show logging`.

#### 4. Enabled logging to syslog server SRV1
- Command: `logging host 192.168.1.100`
- Set logging level to `debugging` (severity 7 – all messages): `logging trap debugging` or `logging trap 7`.
- Verified SRV1 received syslog messages (e.g., interface up/down events).

### Syslog severity levels

| Level | Name | Description |
|-------|------|-------------|
| 0 | Emergency | System unstable |
| 1 | Alert | Immediate action needed |
| 2 | Critical | Critical conditions |
| 3 | Error | Error conditions |
| 4 | Warning | Warning conditions |
| 5 | Notification | Normal but significant |
| 6 | Informational | Informational messages |
| 7 | Debugging | Debug‑level messages |

### Key takeaways

- **Syslog** provides centralised logging for network devices – critical for monitoring and troubleshooting.
- **Severity levels** range from 0 (emergency) to 7 (debugging) – lower numbers are more critical.
- **Console** sees syslog messages by default; **VTY (Telnet/SSH)** sessions do not unless `terminal monitor` is used (or `logging monitor` in real IOS).
- **Buffered logging** stores messages in RAM – viewable with `show logging`.
- **Remote syslog server** collects logs from multiple devices – useful for centralised auditing.
- **Timestamps** help correlate events across devices.

### Verification commands

show logging
show running-config | include logging
terminal monitor (on VTY session)

This lab demonstrated the importance of logging and how to direct logs to different destinations for effective network management.

<img width="918" height="704" alt="Topology Screenshot" src="https://github.com/user-attachments/assets/4a6babf3-96a2-4e45-85a7-3d84f72a4960" />
