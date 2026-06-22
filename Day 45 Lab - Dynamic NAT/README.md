## What I Learned

In this lab, I configured **Dynamic NAT** on R1 with a pool of public IP addresses, then switched to **PAT (Port Address Translation)** using R1's public IP address to allow all three PCs to access the Internet.

### Network overview

| Device | Interface | IP Address | NAT Role |
|--------|-----------|------------|----------|
| PC1 | NIC | 172.16.0.1/24 | Inside local |
| PC2 | NIC | 172.16.0.2/24 | Inside local |
| PC3 | NIC | 172.16.0.3/24 | Inside local |
| R1 G0/1 | 172.16.0.254/24 | Inside interface |
| R1 G0/0 | 203.0.113.1/30 | Outside interface |
| Internet Router | G0/0 | 203.0.113.2/30 | – |
| Server | – | 8.8.8.8 | Internet destination |

### Tasks performed

#### 1. Configured Dynamic NAT on R1
- **Defined inside/outside interfaces:**
  - `interface g0/1` → `ip nat inside`
  - `interface g0/0` → `ip nat outside`

- **Created a NAT pool:**
  - `ip nat pool PUBLIC_POOL 100.0.0.1 100.0.0.2 netmask 255.255.255.0`

- **Defined the ACL to match the internal network:**
  - `access-list 1 permit 172.16.0.0 0.0.0.255`

- **Applied Dynamic NAT:**
  - `ip nat inside source list 1 pool PUBLIC_POOL`

#### 2. Tested Dynamic NAT
- **PC1** pinged `google.com` – succeeded.
- **PC2** pinged `google.com` – succeeded.
- **PC3** pinged `google.com` – **failed**.
  - **Why?** The pool only has **two public IP addresses** (100.0.0.1 and 100.0.0.2). PC1 and PC2 already took them; PC3 has no available public IP to translate to.

#### 3. Cleared NAT and switched to PAT
- **Cleared existing translations:** `clear ip nat translation *`
- **Removed dynamic NAT configuration:**
  - `no ip nat inside source list 1 pool PUBLIC_POOL`
  - (Optional: removed the pool with `no ip nat pool PUBLIC_POOL`)

- **Configured PAT (overload) using R1's public IP:**
  - `ip nat inside source list 1 interface g0/0 overload`

#### 4. Tested PAT
- **PC1, PC2, and PC3** all pinged `google.com` – **successful**.
- **Examined NAT translations** with `show ip nat translations`:
  - All three PCs are mapped to R1's public IP (`203.0.113.1`), distinguished by **different source ports**.
  - Example output:
    - `172.16.0.1:1025 → 203.0.113.1:1025 → 8.8.8.8:80`
    - `172.16.0.2:1026 → 203.0.113.1:1026 → 8.8.8.8:80`
    - `172.16.0.3:1027 → 203.0.113.1:1027 → 8.8.8.8:80`

### Key takeaways

- **Dynamic NAT** maps private IPs to a pool of public IPs – each session consumes a public IP.
- **Pool exhaustion** occurs when more inside hosts than public IPs need access – PC3 failed because the pool had only 2 addresses.
- **PAT (overload)** maps all inside IPs to a single public IP using **different source ports** – saves public IPs.
- **`clear ip nat translation *`** removes dynamic translations; static entries remain.
- **`show ip nat translations`** shows the active translations, including port numbers for PAT.

### Verification commands

show ip nat translations
show ip nat statistics
show access-lists
ping 8.8.8.8

This lab demonstrated the scalability of PAT over Dynamic NAT and why PAT is commonly used in home/enterprise networks.