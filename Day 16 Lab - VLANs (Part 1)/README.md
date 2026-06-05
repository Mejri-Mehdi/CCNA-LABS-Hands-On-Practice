## What I Learned

In this lab, I implemented **legacy inter‑VLAN routing** using three separate physical connections between R1 and SW1 – one for each VLAN. (No trunking, no 802.1Q subinterfaces.)

### VLANs and subnets

| VLAN | Name        | Subnet         | Mask | Usable range                     | Broadcast     |
|------|-------------|----------------|------|----------------------------------|---------------|
| 10   | Engineering | 10.0.0.0       | /26  | 10.0.0.1 – 10.0.0.62             | 10.0.0.63     |
| 20   | HR          | 10.0.0.64      | /26  | 10.0.0.65 - 10.0.0.126           | 10.0.0.127    |
| 30   | Sales       | 10.0.0.128     | /26  | 10.0.0.129 – 10.0.0.190          | 10.0.0.191    |

> **Rule for gateways:** The **last usable IP** of each subnet is assigned to the router’s interface.  
> Example for Sales (`10.0.0.128/26`): last usable = `10.0.0.190`.  
> Example for Engineering (`10.0.0.0/26`): last usable = `10.0.0.62`.

### Tasks performed

1. **Configured PCs** – static IPs from the appropriate subnet, with gateway set to the **last usable address** of that subnet.
2. **Connected R1 to SW1** – three separate cables (one per VLAN).
3. **Configured R1 interfaces** – each interface got the corresponding VLAN’s gateway IP (the last usable address).
4. **Configured SW1**:
   - Created VLANs 10, 20, 30 and named them (`Engineering`, `HR`, `Sales`).
   - Assigned access ports for PCs to their respective VLANs.
   - Assigned the three ports connected to R1 as **access ports** – one in VLAN 10, one in VLAN 20, one in VLAN 30.
5. **Tested connectivity** – PCs in different VLANs could ping each other (routing via R1).
6. **Broadcast ping experiment** – from a PC, pinged its own subnet broadcast address (e.g., `10.0.0.191` for Sales). In Simulation Mode, only devices **within the same VLAN** received the broadcast – confirming Layer 2 broadcast domain separation.

### Key takeaways

- **Legacy inter‑VLAN routing** uses one router physical interface per VLAN – simple but not scalable.
- **Gateway = last usable address** is a clean convention (some use first, but lab requires last).
- **Switch access ports** separate broadcast domains; a broadcast stays inside its VLAN.
- **Simulation Mode** visually proves that a broadcast ping is not forwarded to other VLANs without a router’s help (and a router does not forward layer‑2 broadcasts).

This lab reinforced how VLANs + router interfaces enable inter‑VLAN communication while keeping broadcast traffic isolated.

<img width="832" height="652" alt="Toplogy Screenshot" src="https://github.com/user-attachments/assets/c4481339-2481-4460-b5d4-dc87c5638505" />
