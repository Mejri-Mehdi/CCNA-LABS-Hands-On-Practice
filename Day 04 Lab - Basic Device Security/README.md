# Cisco Device Security Lab – Hostname, Passwords & Encryption

## What I Learned

In this lab, I configured basic security settings on a router and switch using the Cisco IOS CLI.

### Tasks performed

1. **Changed hostnames** – used `hostname R1` and `hostname SW1` in global config mode.
2. **Set an unencrypted enable password** – `enable password CCNA`.
3. **Tested the password** – exited to user EXEC mode (`exit`), then `enable` and entered `CCNA`.
4. **Viewed the password** – `show running-config` (visible as plain text).
5. **Enabled password encryption** – `service password-encryption`.
6. **Verified encryption** – `show running-config` now shows `enable password 7 <encrypted>` (type 7).
7. **Set a more secure enable secret** – `enable secret Cisco`.
8. **Tested access** – after exiting, `enable` asked for the **enable secret** (`Cisco`), not the type 7 password.
9. **Viewed both passwords** – `show running-config` shows:
   - `enable password 7 08026F6028` (type 7 – Cisco’s weak reversible encryption)
   - `enable secret 5 $1$mERr$Y1CkLMcTYWwkF1Cndtl1.` (type 5 – MD5 hash, stronger)
10. **Saved configuration** – `write memory` or `copy running-config startup-config`.

### Key takeaways

- **`enable password`** (type 7) is weakly encrypted and easily decrypted – avoid in production.
- **`enable secret`** (type 5) uses MD5 hashing and is the preferred privileged mode password.
- If both are configured, only the **enable secret** is accepted.
- **`service password-encryption`** automatically encrypts new passwords (type 7), but does **not** affect existing `enable secret`.
- Always **save your config** with `write memory` or `copy running-config startup-config`.

This lab reinforced the importance of strong password storage and proper device hardening.

<img width="2428" height="687" alt="Topology Screenshot" src="https://github.com/user-attachments/assets/45cfd0c0-dd76-456c-bf90-bcec8186dc79" />
