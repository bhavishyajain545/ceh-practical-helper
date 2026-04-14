# Q030 — SMTP VRFY User Enum

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `telnet (or nc)` |
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
Pre-requisites for Q030:
1. Verify Metasploitable2 (192.168.52.129) up; postfix running: ssh msfadmin@192.168.52.129 → sudo service postfix status (start if down).
2. From Parrot: nmap -p 25 192.168.52.129 — open.
3. On Parrot, telnet client available: which telnet (sudo apt install telnet if missing).

Report back: "Lab ready for Q030 — SMTP 25 open on 192.168.52.129, VRFY enabled".
```
