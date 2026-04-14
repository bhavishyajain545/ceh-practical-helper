# Q078 — phpMyAdmin Default Login

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🔴 Hard |
| **Tools** | `burpsuite`, `sqlmap`, `gobuster`, `nikto`, `curl` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Login to Metasploitable phpMyAdmin at `/phpMyAdmin/`. What credentials work?

---

## 🎯 Flag Format

```
user=<u>; pass=<p>
```

Example: `user=root; pass=`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Blank root password.
</details>

<details>
<summary>Hint 2</summary>

Try root with no password.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# http://192.168.52.129/phpMyAdmin/ → user=root, pass=(blank)
```

Logs in as root.

**Answer:** `user=root; pass=`

📖 Ref: [tools/default-creds.md](../../tools/default-creds.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q078".
```
