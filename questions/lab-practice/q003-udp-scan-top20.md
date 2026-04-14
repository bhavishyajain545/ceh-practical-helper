# Q003 — UDP Top-20 Scan

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Scan the top 20 UDP ports on Metasploitable (192.168.52.129). Identify how many UDP ports report `open|filtered` or `open`, and name the service running on **port 53**.

---

## 🎯 Flag Format

```
udp_open=<n>; port53_service=<name>
```

Example: `udp_open=4; port53_service=domain`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`sudo nmap -sU --top-ports 20 <ip>` — UDP needs root.
</details>

<details>
<summary>Hint 2</summary>

Service name lookup is in `/etc/services` or via `-sV`.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo nmap -sU --top-ports 20 192.168.52.129
```

Common open|filtered: 53, 68, 69, 111, 137, 2049 etc. Port 53 = `domain` (DNS).

**Answer:** `udp_open=6; port53_service=domain`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q003:
1. Verify Metasploitable2 (192.168.52.129) is reachable: ping -c 2 192.168.52.129.
2. UDP scan requires raw sockets — confirm sudo works on Parrot: sudo -n true; else cache creds with sudo -v.
3. Confirm the UDP listeners expected in the answer are up on Metasploitable:
   - DNS (53): sudo service bind9 status
   - NFS/portmap (111, 2049): sudo service portmap status; sudo service nfs-kernel-server status
   - NetBIOS (137): sudo service samba status (nmbd)
4. UDP scans are slow — note that --top-ports 20 with -sU typically takes 30-90s even on LAN.

Report back: "Lab ready for Q003 — 192.168.52.129 reachable, sudo works, DNS/NFS/samba up".
```
