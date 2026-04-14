# Q084 — Session Cookie Hijack

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `burpsuite`, `sqlmap`, `gobuster`, `nikto`, `curl` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Capture DVWA `PHPSESSID` cookie via stored XSS, replay from another browser. What HTTP header carries the cookie?

---

## 🎯 Flag Format

```
header=<name>
```

Example: `header=Cookie`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

All cookies travel in `Cookie:` header.
</details>

<details>
<summary>Hint 2</summary>

Use Burp to inject the stolen cookie.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# Replay with Cookie: PHPSESSID=stolen
```

Authenticated session hijacked.

**Answer:** `header=Cookie`

📖 Ref: [playbooks/session-hijacking-playbook.md](../../playbooks/session-hijacking-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q084".
```
