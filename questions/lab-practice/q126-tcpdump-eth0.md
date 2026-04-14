# Q126 — tcpdump on eth0

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` / pcap |
| **Domain** | 07 — Sniffing / 14 — Wireless |
| **Difficulty** | 🟢 Easy |
| **Tools** | `tcpdump`, `wireshark`, `ettercap`, `bettercap`, `aircrack-ng` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Capture 50 packets of HTTP traffic on Parrot eth0 destined to Metasploitable. Provide command.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=sudo tcpdump -i eth0 -c 50 'tcp port 80 and host 192.168.52.129'`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

BPF filters in single quotes.
</details>

<details>
<summary>Hint 2</summary>

Save to pcap with `-w`.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo tcpdump -i eth0 -c 50 'tcp port 80 and host 192.168.52.129' -w http.pcap
```

Saves 50 packets.

**Answer:** `cmd=sudo tcpdump -i eth0 -c 50 'tcp port 80 and host 192.168.52.129'`

📖 Ref: [playbooks/sniffing-playbook.md](../../playbooks/sniffing-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q126".
```
