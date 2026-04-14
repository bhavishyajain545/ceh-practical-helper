# Q003 — UDP Top-20 Scan

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap`, `hping3`, `masscan`, `nc` |
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
1. Metasploitable up; DNS/NFS/SMB services running.
2. Verify root on Parrot for UDP scan.

Report back: "Lab ready for Q003".
```
