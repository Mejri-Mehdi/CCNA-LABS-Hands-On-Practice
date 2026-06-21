# SSH & Remote Access Security Lab – Packet Tracer

## What I Learned

In this lab, I configured a newly added switch (SW2) with **console line security** and **remote access via SSH**. I also restricted SSH access to only PC1 using an ACL.

### Network overview

| Device | Interface | IP Address | Role |
|--------|-----------|------------|------|
| PC1 | 192.168.1.1/24 | SSH client (allowed access) |
| SW1 VLAN1 | 192.168.1.253/24 | Layer 2 connectivity |
| R1 G0/1 | 192.168.1.254/24 | Connects to SW1 |
| R1 G0/0 | 10.0.0.1/30 | Link to R2 |
| R2 G0/0 | 10.0.0.2/30 | Link to R1 |
| R2 G0/1 | 192.168.2.254/24 | Connects to SW2 |
| SW2 VLAN1 | 192.168.2.253/24 | SVI (management interface) |
| PC2 | 192.168.2.1/24 | SSH client (blocked access) |

> SW2 is **newly added** and had **no configuration** initially.

### Tasks performed

#### 1. Configured basic settings on SW2 (via console port from Laptop1)

| Setting | Value |
|---------|-------|
| Hostname | SW2 |
| Enable secret | `ccna` |
| Username/Password | `jeremy` / `ccna` |
| VLAN1 SVI | `192.168.2.253/24` |
| Default gateway | `192.168.2.254` (R2) |

Commands:

hostname SW2
enable secret ccna
username jeremy secret ccna
interface vlan 1
ip address 192.168.2.253 255.255.255.0
no shutdown
ip default-gateway 192.168.2.254

#### 2. Configured console line security

- **Authentication**: Local user – `line console 0` → `login local`
- **Exec timeout**: 5 minutes – `exec-timeout 5 0`

line console 0
login local
exec-timeout 5 0

#### 3. Configured SW2 for remote access via SSH

| Setting | Value |
|---------|-------|
| Domain name | `jeremysitlab.com` |
| RSA key size | 2048 bits |
| Authentication | Local user |
| Exec timeout | 5 minutes |
| Protocols | SSH only (no Telnet) |
| Access restriction | PC1 **only** |

**Commands:**

ip domain-name jeremysitlab.com
crypto key generate rsa modulus 2048
username jeremy secret ccna
line vty 0 15
login local
exec-timeout 5 0
transport input ssh
exit

#### 4. Limited SSH access to PC1 ONLY

- Created a **standard ACL** permitting only PC1's IP (`192.168.1.1`):

access-list 1 permit host 192.168.1.1

- Applied the ACL to the VTY lines:

line vty 0 15
access-class 1 

### Verification

- From **PC1** (`192.168.1.1`): SSH to SW2 (`192.168.2.253`) **succeeds**.
- From **PC2** (`192.168.2.1`): SSH to SW2 **fails** (permission denied due to ACL).
- Console access still works via the physical console port.

### Key takeaways

- **SSH** is more secure than Telnet – encrypts all traffic (including passwords).
- **RSA key** must be generated **before** enabling SSH (`crypto key generate rsa`).
- **Access-class** restricts which IPs can connect to VTY lines – essential for security.
- **Local user** authentication (`login local`) uses the username/password database on the switch.
- **Exec timeout** prevents idle sessions from staying open indefinitely.
- **Domain name** is required for RSA key generation (even if not used for certificates).

### Verification commands

show ssh
show ip ssh
show access-lists
show running-config | include ssh
show users

This lab demonstrated how to securely configure a switch for remote management using SSH, with strict access control.