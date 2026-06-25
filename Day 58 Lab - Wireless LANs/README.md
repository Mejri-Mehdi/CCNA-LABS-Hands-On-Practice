## What I Learned

In this lab, I accessed the **Wireless LAN Controller (WLC)** GUI via HTTPS, explored its interface, configured **dynamic interfaces** for multiple WLANs, created **WPA2+PSK** secured wireless networks, and added a wireless client to associate with an AP.

### Network overview

| Device | Interface | IP Address | VLAN |
|--------|-----------|------------|------|
| WLC1 Management | 172.16.1.10/24 | VLAN10 (Management) |
| Internal WLAN | 10.0.0.0/24 | VLAN100, SSID = Internal |
| Guest WLAN | 10.1.0.0/24 | VLAN200, SSID = Guest |
| AP1 | – | Connected to WLC1 (G1/0/1) |
| AP2 | – | Connected to WLC1 (G1/0/2) |
| PC1 | – | Management access to WLC |
| Smartphone0 | – | Wireless client |

### Tasks performed

#### 1. Accessed WLC1 GUI via web browser on PC1
- Opened web browser on PC1 (Desktop → Web Browser).
- Entered WLC1's management IP: `https://172.16.1.10` (used HTTPS as instructed).
- Username: `admin` | Password: `Cisco123`

#### 2. Explored the WLC GUI
- Spent time navigating the GUI to understand its layout:
  - **Monitor** – dashboard showing AP status, client counts, and overall network health.
  - **WLANs** – view/create/modify wireless networks.
  - **Controller** – interface configuration, AP management, and high availability.
  - **Wireless** – AP details, client tracking.
  - **Security** – AAA, encryption, authentication settings.
  - **Management** – system settings, SNMP, user accounts.
  - **Commands** – ping, traceroute, debug tools.
- Determined the current state of the network: APs registered, VLANs configured, etc.

#### 3. Configured dynamic interfaces for Internal & Guest WLANs
- **WLC GUI Path:** Controller → Interfaces → New
- **Created Internal interface:**
  - Interface Name: `Internal`
  - VLAN ID: `100`
  - IP Address: `10.0.0.1/24`
  - DHCP: configured or external server
- **Created Guest interface:**
  - Interface Name: `Guest`
  - VLAN ID: `200`
  - IP Address: `10.1.0.1/24`
  - DHCP: configured or external server

#### 4. Created Internal & Guest WLANs with WPA2+PSK
- **WLC GUI Path:** WLANs → Create New → Apply
- **Internal WLAN:**
  - Profile Name: `Internal`
  - SSID: `Internal`
  - Security: WPA2+PSK
  - PSK: (provided passphrase)
  - Interface: `Internal`
- **Guest WLAN:**
  - Profile Name: `Guest`
  - SSID: `Guest`
  - Security: WPA2+PSK
  - PSK: (provided passphrase)
  - Interface: `Guest`
- Verified both WLANs were enabled and broadcasting.

#### 5. Added a wireless client and associated with AP
- Added a **Smartphone** (or wireless laptop) to the topology.
- Scanned for available wireless networks.
- Connected to the **Internal** SSID using the configured PSK.
- Verified the client received an IP from the Internal VLAN/subnet.
- Confirmed association in WLC GUI: **Wireless → Clients** showing the connected client.

### Key takeaways

- **WLC** centrally manages multiple lightweight APs (LAPs), simplifying deployment and management.
- **Dynamic interfaces** map WLANs to VLANs and subnets – each WLAN can have its own IP addressing and security policy.
- **WPA2+PSK** provides secure, password‑based authentication for wireless clients.
- **HTTPS access** ensures secure management of the WLC.
- The GUI provides complete visibility into AP status, client connections, and network performance.
- **Lightweight APs** are "dumb" – they rely on the WLC for configuration, firmware, and encryption.

### Verification commands (GUI-based, not CLI)

- **Monitor → Clients** – verify client association and IP address.
- **Wireless → All APs** – check AP registration status.
- **Controller → Interfaces** – verify dynamic interface IP and VLAN assignment.

This lab introduced wireless network management using a centralized controller and Lightweight Access Points.

<img width="818" height="715" alt="Topology Screenshot" src="https://github.com/user-attachments/assets/6fa64ef4-8245-48b7-a4a1-d8249174c581" />
