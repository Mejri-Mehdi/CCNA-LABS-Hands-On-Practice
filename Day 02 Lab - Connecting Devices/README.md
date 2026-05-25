## What I Learned

In this lab, I connected network devices based on a labeled topology.  
Key constraints:

- **Auto MDI-X disabled** – had to choose the correct cable type manually.
- **Different distances** – used appropriate physical layer media:
  - `R1–R3`: **3 km** → fiber optic (single‑mode fiber is appropriate for long distances)
  - `R1–R2`: **50 m** → copper crossover (since Auto MDI‑X is off, and both ends are routers)
  - Switch‑to‑switch & switch‑to‑PC connections: straight‑through or crossover as needed
- **Router R4** connects to **many switches** (SW5 through SW193) – practiced scalability and consistent cabling.

### Key takeaways

- **Cable selection matters** – fiber for long distances, copper for short links; crossover vs straight‑through depends on device types when Auto MDI‑X is absent.
- **Distance awareness** – 3 km forces fiber; 50 m can use copper but must handle potential signal degradation.
- **Manual cabling discipline** – without auto‑detection, you must know which pairs are used for transmit/receive.
- **Large‑scale connections** – connecting dozens of switches to a single router reinforces structured cabling and port management.

This lab strengthened my understanding of physical layer requirements and proper cable choice in real‑world networks.

<img width="1673" height="645" alt="Topology Screenshot" src="https://github.com/user-attachments/assets/d1f0604c-7f9d-4f46-9b60-5d7f7d0548c4" />