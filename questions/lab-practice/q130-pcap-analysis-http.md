# Q130 — PCAP Analysis HTTP Creds

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` / pcap |
| **Domain** | 07 — Sniffing / 14 — Wireless |
| **Difficulty** | 🟡 Medium |
| **Tools** | `tshark` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Open `auth.pcap` (lab-provided) in Wireshark and find the FTP password sent. Provide CLI alternative using tshark.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=tshark -r auth.pcap -Y 'ftp.request.command=="PASS"' -T fields -e ftp.request.arg`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

tshark display filter `-Y` and field extract `-e`.
</details>

<details>
<summary>Hint 2</summary>

Or `strings` on pcap for quick wins.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
tshark -r auth.pcap -Y 'ftp.request.command=="PASS"' -T fields -e ftp.request.arg
```

Prints captured passwords.

**Answer:** `cmd=tshark -r auth.pcap -Y ftp.request.command==PASS`

📖 Ref: [playbooks/sniffing-playbook.md](../../playbooks/sniffing-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q130:
1. Stage auth.pcap on Parrot — generate via: sudo tcpdump -i eth0 -w auth.pcap 'port 21' & ftp 192.168.52.129 (login user:user) then stop capture.
2. which tshark (part of wireshark-cli).

Report back: "Lab ready for Q130 — tshark installed, auth.pcap with FTP PASS captured".
```
