# Q127 — Wireshark Credential Filter

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` / pcap |
| **Domain** | 07 — Sniffing / 14 — Wireless |
| **Difficulty** | 🟢 Easy |
| **Tools** | `tcpdump`, `wireshark`, `ettercap`, `bettercap`, `aircrack-ng` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Provide a Wireshark display filter for capturing FTP USER/PASS commands.

---

## 🎯 Flag Format

```
filter=<text>
```

Example: `filter=ftp.request.command == "USER" or ftp.request.command == "PASS"`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

FTP is plaintext.
</details>

<details>
<summary>Hint 2</summary>

Use Statistics → Conversations.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# In Wireshark filter bar
```

Shows USER/PASS frames.

**Answer:** `filter=ftp.request.command == "USER" or ftp.request.command == "PASS"`

📖 Ref: [playbooks/sniffing-playbook.md](../../playbooks/sniffing-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q127".
```
