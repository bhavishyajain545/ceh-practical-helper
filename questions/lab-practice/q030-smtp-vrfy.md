# Q030 — SMTP VRFY User Enum

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `enum4linux`, `smbclient`, `rpcclient`, `nmap NSE` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Connect to Metasploitable SMTP (25) and run `VRFY root` and `VRFY nonexistent`. Report the response codes.

---

## 🎯 Flag Format

```
vrfy_root=<code>; vrfy_fake=<code>
```

Example: `vrfy_root=252; vrfy_fake=550`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`telnet 192.168.52.129 25` then `VRFY <user>`.
</details>

<details>
<summary>Hint 2</summary>

Postfix returns 252 for known, 550 for unknown.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
telnet 192.168.52.129 25
# VRFY root
# VRFY xxxxxxx
```

252 = exists/cannot verify; 550 = no such user.

**Answer:** `vrfy_root=252; vrfy_fake=550`

📖 Ref: [playbooks/enumeration-playbook.md](../../playbooks/enumeration-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q030".
```
