## What I Learned

In this lab, I configured IP addresses and routing on R1 and R2, then used **TFTP** (Trivial File Transfer Protocol) and **FTP** (File Transfer Protocol) to retrieve an IOS image from SRV1 and upgrade the routers' operating systems.

### Network overview

| Device | Interface | IP Address |
|--------|-----------|------------|
| R1 G0/1 | 10.0.0.254/24 | Connects to SW1 (SRV1) |
| R1 G0/0 | 10.0.0.1/24 | Connects to R2 |
| R2 G0/0 | 10.0.0.2/24 | Connects to R1 |
| SW1 | 10.0.0.1/24 | Layer 2 connectivity |
| SRV1 | 10.0.0.1/24 | TFTP/FTP server |

### Tasks performed

#### 1. Basic configuration
- Configured IP addresses on all devices.
- Configured **routing** (static routes or OSPF) to allow full connectivity between all devices.
- Verified connectivity with `ping` (e.g., R1 → SRV1, R2 → SRV1).

#### 2. TFTP file transfer from R1 to SRV1
- On R1, used TFTP to retrieve the IOS file from SRV1:
  - Command: `copy tftp://10.0.0.1/c2900-universalk9-mz.SPA.155-3.M4a.bin flash:`
  - (Or `copy tftp flash` and enter the server IP and filename manually.)
- Verified the file was successfully copied to flash with `show flash`.

#### 3. Upgraded R1's OS and deleted the old file
- Changed the boot statement to load the new IOS:
  - `boot system flash:c2900-universalk9-mz.SPA.155-3.M4a.bin`
- Reloaded R1 with `reload`.
- After reload, confirmed the new IOS version with `show version`.
- Deleted the old IOS file from flash:
  - `delete flash:<old_ios_filename>`
  - (Packet Tracer may not allow deletion of the running image; in real devices, ensure the new image is set as the boot image first.)

#### 4. FTP file transfer from R2 to SRV1
- On R2, used FTP to retrieve the same IOS file:
  - Command: `copy ftp://jeremy:ccna@10.0.0.1/c2900-universalk9-mz.SPA.155-3.M4a.bin flash:`
  - The transfer may take about a minute (as noted in the lab instructions).
- Verified the file with `show flash`.

#### 5. Upgraded R2's OS and deleted the old file
- Changed the boot statement:
  - `boot system flash:c2900-universalk9-mz.SPA.155-3.M4a.bin`
- Reloaded R2 with `reload`.
- Confirmed the new IOS version with `show version`.
- Deleted the old IOS file from flash.

### Key takeaways

- **TFTP** (UDP port 69) is simple and lightweight – no authentication, suitable for quick file transfers.
- **FTP** (TCP ports 20/21) supports authentication and is more reliable than TFTP, but slower.
- **IOS upgrade process**: transfer file → set boot statement → reload → verify → delete old file.
- Always keep a **backup** of the current IOS before upgrading in production.
- **`show flash`** displays available files and space; **`show version`** confirms the running IOS version.
- **Deleting the old file** frees up flash space, but ensure the new image works before doing so.

### Verification commands

show flash
show version
show running-config | include boot system
dir flash:

This lab introduced IOS file management and upgrade procedures using both TFTP and FTP.