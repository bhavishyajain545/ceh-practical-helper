# Q089 — File Upload MIME Bypass

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `burpsuite`, `sqlmap`, `gobuster`, `nikto`, `curl` |
| **Time budget** | 15–25 min |

---

## 📝 Question

On DVWA medium upload, the page checks MIME type. Provide a method to upload PHP shell with `Content-Type: image/jpeg` header.

---

## 🎯 Flag Format

```
tool=<name>
```

Example: `tool=Burp Suite`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Intercept request, change Content-Type.
</details>

<details>
<summary>Hint 2</summary>

Or rename `.php.jpg` for double-extension trick (some servers).
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# Burp intercept; modify Content-Type: image/jpeg; keep .php name
```

Server accepts upload as image; PHP still executes.

**Answer:** `tool=Burp Suite`

📖 Ref: [playbooks/webapp-playbook.md](../../playbooks/webapp-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q089".
```
