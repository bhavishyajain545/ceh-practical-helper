# Q130 — PCAP Analysis HTTP Creds

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` / pcap |
| **Domain** | 07 — Sniffing / 14 — Wireless |
| **Difficulty** | 🟡 Medium |
| **Tools** | `tcpdump`, `wireshark`, `ettercap`, `bettercap`, `aircrack-ng` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q130".
```
