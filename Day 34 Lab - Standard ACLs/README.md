## What I Learned

In this lab, I configured **OSPF** on R1 and R2 to provide full connectivity between all PCs and servers. Then I implemented **standard ACLs** (numbered on R1, named on R2) to enforce specific network policies.

### Network policies to enforce

| # | Policy |
|---|--------|
| 1 | Only **PC1** and **PC3** can access `192.168.1.0/24` |
| 2 | Hosts in `172.16.2.0/24` **cannot** access `192.168.2.0/24` |
| 3 | `172.16.1.0/24` **cannot** access `172.16.2.0/24` |
| 4 | `172.16.2.0/24` **cannot** access `172.16.1.0/24` |

### Tasks performed

#### 1. OSPF configuration
- Enabled OSPF on R1 and R2 (process ID, network statements, or interface‑based OSPF).
- Verified full reachability between all PCs and servers with `ping`.

#### 2. Standard numbered ACLs on R1
- Used ACL numbers `1–99` (standard IP).
- **Policy 1** – `permit` only PC1 and PC3, `deny` all others to `192.168.1.0/24`:

access-list 1 permit host <PC1_IP>
access-list 1 permit host <PC3_IP>
access-list 1 deny any

- **Policy 2** – `deny` `172.16.2.0/24` from reaching `192.168.2.0/24`:

access-list 2 deny 172.16.2.0 0.0.0.255
access-list 2 permit any

- Applied to appropriate interfaces **inbound** or **outbound** using `ip access-group <acl> in/out`.

#### 3. Standard named ACLs on R2
- Created named standard ACLs: `ip access-list standard <name>`.
- Implemented **Policy 3** – block `172.16.1.0/24` → `172.16.2.0/24`:

ip access-list standard BLOCK_1_TO_2
deny 172.16.1.0 0.0.0.255
permit any

- Implemented **Policy 4** – block `172.16.2.0/24` → `172.16.1.0/24`:

ip access-list standard BLOCK_2_TO_1
deny 172.16.2.0 0.0.0.255
permit any

- Applied named ACLs to interfaces using `ip access-group <name> in/out`.

#### 4. Verification
- Tested each policy with pings from the source to the destination to confirm:
- PC1 ✅ can access `192.168.1.0/24`
- PC3 ✅ can access `192.168.1.0/24`
- Other PCs ❌ cannot access `192.168.1.0/24`
- `172.16.2.0/24` ❌ cannot access `192.168.2.0/24`
- `172.16.1.0/24` ↔ `172.16.2.0/24` ❌ blocked both directions

### Key takeaways

- **Standard ACLs** filter based **only on source IP address** – they are simple but limited.
- **Numbered ACLs** (`1–99`) are the traditional method; **named ACLs** provide better readability.
- ACLs are applied **inbound** (traffic entering the interface) or **outbound** (traffic leaving) – placement matters.
- **Implicit deny** at the end of every ACL – must explicitly permit necessary traffic.
- OSPF provides the underlying reachability; ACLs control which traffic is allowed.

### Verification commands

- Applied named ACLs to interfaces using `ip access-group <name> in/out`.

#### 4. Verification
- Tested each policy with pings from the source to the destination to confirm:
- PC1 ✅ can access `192.168.1.0/24`
- PC3 ✅ can access `192.168.1.0/24`
- Other PCs ❌ cannot access `192.168.1.0/24`
- `172.16.2.0/24` ❌ cannot access `192.168.2.0/24`
- `172.16.1.0/24` ↔ `172.16.2.0/24` ❌ blocked both directions

### Key takeaways

- **Standard ACLs** filter based **only on source IP address** – they are simple but limited.
- **Numbered ACLs** (`1–99`) are the traditional method; **named ACLs** provide better readability.
- ACLs are applied **inbound** (traffic entering the interface) or **outbound** (traffic leaving) – placement matters.
- **Implicit deny** at the end of every ACL – must explicitly permit necessary traffic.
- OSPF provides the underlying reachability; ACLs control which traffic is allowed.

### Verification commands

show ip access-lists
show ip interface brief
show ip ospf neighbor
ping <destination_IP>

This lab reinforced access control implementation with both numbered and named standard ACLs in a routed OSPF network.

<img width="1212" height="672" alt="Topology Screenshot" src="https://github.com/user-attachments/assets/e0db75c3-e256-4eb3-ad83-00d9569d3437" />
