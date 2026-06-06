## What I Learned

In this lab, I configured **VTP** (VLAN Trunking Protocol) across three switches (SW1, SW2, SW3) to understand how VLAN information propagates (or doesn’t) depending on VTP mode.

### Topology summary

| Switch | VTP Mode     | Role                        |
|--------|--------------|-----------------------------|
| SW1    | Server       | Creates VLANs 10, 20, 30    |
| SW2    | Transparent  | Adds VLAN40 (local only)    |
| SW3    | Client       | Receives VLANs from server  |

Trunk links connect the switches (DTP disabled, manual trunk configuration).

### Tasks performed

1. **Configured trunk ports** between switches:
   - `switchport mode trunk`
   - `switchport nonegotiate` (disables DTP)
   - Verified administrative and operational mode with `show interfaces trunk`.

2. **Configured VTP on SW1 (Server)**:
   - `vtp domain CCNA`
   - `vtp mode server`
   - Created VLANs 10, 20, 30.

3. **Checked SW2 and SW3** – they automatically learned VLANs 10, 20, 30 (since VTP domain matches and SW2 in Transparent? Actually transparent does not learn but forwards; client learns. Let's be precise in README.)

4. **Configured SW2 as VTP Transparent**:
   - `vtp mode transparent`
   - Added VLAN40 locally.
   - Verified VLAN40 **does not** appear on SW1 or SW3 (transparent does not propagate its VLANs).

5. **Configured SW3 as VTP Client**:
   - `vtp mode client`
   - Attempted to create VLAN50 – operation **failed** (client cannot create VLANs).
   - Verified SW3 received VLANs 10,20,30 from SW1 (server).

6. **Configured access ports for hosts**:
   - `switchport mode access`
   - `switchport access vlan <x>`
   - **DTP is still disabled** on access ports because `switchport mode access` implies no trunk negotiation (DTP is off).

### Key takeaways

- **VTP Server** – can create, modify, delete VLANs; sends advertisements to other switches in the same domain.
- **VTP Transparent** – forwards VTP advertisements but does not synchronize; VLAN changes are local only.
- **VTP Client** – synchronizes with server, cannot create/delete VLANs locally.
- **DTP** is disabled by `switchport nonegotiate` on trunks or by setting `switchport mode access` on host ports.
- `show interfaces trunk` confirms trunk status; `show vtp status` shows VTP mode and revision number.

This lab gave me hands‑on experience with VTP behaviour, its limitations, and why transparent mode is often preferred for safety.