# Q010 — Scan Windows 7 Target

| Field | Value |
|---|---|
| **Target** | `192.168.52.130` (Windows 7) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Run a SYN+version scan on Windows 7 (192.168.52.130) and identify whether **port 445 (SMB)** is open. Also report the OS detected.

---

## 🎯 Flag Format

```
smb445=<open|closed>; os=<windows7|windows10>
```

Example: `smb445=open; os=windows7`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`sudo nmap -sS -sV -O <ip>`
</details>

<details>
<summary>Hint 2</summary>

445/tcp microsoft-ds typically open by default on Win7.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo nmap -sS -sV -O 192.168.52.130
```

Port 445 open; OS = Windows 7.

**Answer:** `smb445=open; os=windows7`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q010:
1. Win7 VM (192.168.52.130) must be powered on and on the same VMnet as Parrot.
2. Windows Firewall on Win7 blocks ICMP and SMB by default. On the Win7 VM console, set the lab NIC profile to "Home/Work" AND either:
   (a) Turn Windows Firewall OFF for the lab profile: Control Panel → System and Security → Windows Firewall → Turn Windows Firewall on or off → Off (Private network only). OR
   (b) Enable the specific inbound rules: "File and Printer Sharing (SMB-In)" and "File and Printer Sharing (Echo Request - ICMPv4-In)" for the Private profile.
3. Enable SMBv1 + File and Printer Sharing so port 445 is actually listening:
   - Control Panel → Programs → Turn Windows features on or off → check "SMB 1.0/CIFS File Sharing Support".
   - Network and Sharing Center → Advanced sharing settings → Turn on file and printer sharing.
4. Verify from Parrot: ping -c 2 192.168.52.130 (should reply), then nc -vz 192.168.52.130 445 (should say "succeeded").
5. -O requires raw packets — sudo required on Parrot.

Report back: "Lab ready for Q010 — 192.168.52.130 pings, port 445 open, sudo works".
```
