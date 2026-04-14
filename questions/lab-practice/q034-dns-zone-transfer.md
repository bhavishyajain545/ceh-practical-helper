# Q034 — DNS Zone Transfer Attempt (lab)

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟡 Medium |
| **Tools** | `enum4linux`, `smbclient`, `rpcclient`, `nmap NSE` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Use `dig @192.168.52.129 axfr example.local` against Metasploitable's DNS (if running). Report whether transfer succeeds.

---

## 🎯 Flag Format

```
axfr=<allowed|refused|no_dns>
```

Example: `axfr=no_dns`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Metasploitable doesn't run a DNS server by default.
</details>

<details>
<summary>Hint 2</summary>

Confirm port 53 TCP state with nmap.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nmap -p 53 192.168.52.129
dig @192.168.52.129 axfr example.local
```

Port 53 may be open via dnsmasq fragments — usually no zone available; expect `no_dns` or refused.

**Answer:** `axfr=no_dns`

📖 Ref: [tools/dig.md](../../tools/dig.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q034".
```
