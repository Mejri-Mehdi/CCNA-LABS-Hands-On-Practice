## What I Learned

In this lab, I analyzed an existing spanning‑tree topology, identified the root bridge, examined unexpected port behavior, and manually configured RSTP link types.

### Lab setup (from diagram)

Four switches: **SW1, SW2, SW3, SW4** with redundant interconnections.  
Bridge priorities and MAC addresses (as shown in the image):

| Switch | Priority | MAC Address |
|--------|----------|-------------|
| SW1    | 32769    | 0005.5E4E714B |
| SW2    | 32769    | 0000.5882.4834 |
| SW3    | 32769    | 0000.5B19.5EBA |
| SW4    | 32769    | 0090.0C01.9587 |

### Questions answered

1. **Which switch is the root bridge?**  
   `SW1` – all switches have the same priority (32769), so the lowest MAC address determines the root. SW1’s MAC (`0005.5E4E714B`) is the lowest among the four.

2. **Examining the root bridge’s ports via CLI** (`show spanning-tree`):  
   On a normal root bridge, **all ports should be in Designated (FWD) state**.  
   **What appeared different?** – Possibly some ports were in **Blocking** or **Alternate** state.  
   **Cause:** STP still needs to block ports even on the root bridge if there are **multiple parallel links to the same neighbour** (e.g., two links between SW1 and SW2). In that case, one of them becomes non‑designated to prevent a loop. This is normal but surprises many learners.

3. **Determining port roles without CLI** – used the STP election rules (lowest path cost to root, lowest sender bridge ID, lowest sender port ID). Then confirmed with `show spanning-tree`.

4. **Manually configuring RSTP link types** on each interface:  
   - `spanning-tree link-type point-to-point` for full‑duplex links between switches.  
   - `spanning-tree link-type shared` for half‑duplex or hub segments.  
   - For **SW1’s F0/24** (likely connected to another switch), the correct link type is **point‑to‑point** (assuming a direct switch‑to‑switch connection in full duplex). RSTP uses this to enable fast transition to forwarding.

### Key takeaways

- **Root bridge election:** lowest bridge ID (priority + MAC). When priorities are equal, the lowest MAC wins.
- **Root bridge can have blocking ports** if multiple links exist to the same neighbour – this is normal behaviour.
- **RSTP link types** affect how quickly a port transitions to forwarding:
  - `point-to-point` → allows **rapid transition** (no waiting for listening/learning).
  - `shared` → falls back to 802.1D slow convergence.
- Always confirm STP topology with `show spanning-tree` and `show spanning-tree interface detail`.

This lab deepened my understanding of STP edge cases and RSTP optimisation.

<img width="771" height="645" alt="Topology Screenshot" src="https://github.com/user-attachments/assets/8fe3684f-4524-42f7-adfb-7f68d0df932c" />
