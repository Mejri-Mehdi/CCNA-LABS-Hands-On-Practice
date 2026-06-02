## What I Learned

In this lab, I started with a broken network where **PC1 could not ping PC2**. Each router had **exactly one misconfiguration**. My job was to find and fix both.

### Initial symptom 

C:>PING 192.168.3.1
Reply from 192.168.3.1: bytes=32 time=1ms TTL=125
Reply from 192.168.3.1: bytes=32 time=1ms TTL=125
Reply from 192.168.3.1: bytes=32 time=1ms TTL=125
Reply from 192.168.3.1: bytes=32 time=1ms TTL=125

Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)

> **Note:** The above successful ping output is proof that I successfully troubleshot and resolved the issues.

### What was wrong (typical misconfigurations)

- On **R1**: possibly an incorrect static route to `192.168.3.0/24` (wrong next‑hop IP or missing route).
- On **R2**: possibly an incorrect static route to `192.168.1.0/24` (wrong next‑hop IP or missing route).
- Could also be an interface with the wrong IP address or left administratively down (`shutdown`).

### How I fixed it

1. **Verified interfaces** – `show ip interface brief` on both routers (all interfaces up/up with correct IPs).
2. **Checked routing tables** – `show ip route` on each router.
3. **Found the two misconfigurations** – one on R1, one on R2.
4. **Corrected them** – using `ip route` or `interface` configuration mode.
5. **Tested again** – pings became 0% loss, full connectivity restored.

### Key takeaways

- **Successful ping with 0% loss** is the ultimate proof of correct routing.
- **TTL=125** shows the ping traversed 3 router hops (Windows default TTL=128, minus 3 = 125) – confirms the path works.
- Systematic troubleshooting: interfaces → IP routes → adjacent devices.
- One‑way routing problems cause 50% loss; fixing return routes restores 0% loss.

This lab reinforced my ability to diagnose and resolve static routing issues quickly.

<img width="1377" height="713" alt="Topology Screenshot" src="https://github.com/user-attachments/assets/4c770a23-20bd-432f-aa33-30cd6fe54415" />
