# Q080 — Burp Suite Intercept

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `burpsuite`, `sqlmap`, `gobuster`, `nikto`, `curl` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Configure Burp to intercept DVWA login request. What **request method** and what is the **POST parameter** containing the password?

---

## 🎯 Flag Format

```
method=<POST|GET>; param=<name>
```

Example: `method=POST; param=password`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Set browser proxy to 127.0.0.1:8080.
</details>

<details>
<summary>Hint 2</summary>

DVWA login uses POST.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# Burp → Proxy → Intercept ON; submit DVWA login
```

Captures POST with `username` and `password` params.

**Answer:** `method=POST; param=password`

📖 Ref: [tools/burpsuite.md](../../tools/burpsuite.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q080".
```
