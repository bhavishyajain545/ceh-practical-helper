# Q084 — Session Cookie Hijack

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `curl, browser` |
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
Pre-requisites for Q084:
1. Requires DVWA (Q067 prereqs) + a stolen PHPSESSID cookie from Q070 XSS.
2. Two browsers (or Firefox+curl) on Parrot — one authenticated (supplies cookie), one as attacker replaying it.

Report back: "Lab ready for Q084 — DVWA up, two browser contexts available".
```
