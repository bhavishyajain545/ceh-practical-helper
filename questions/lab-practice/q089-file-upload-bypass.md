# Q089 — File Upload MIME Bypass

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `burpsuite` |
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
Pre-requisites for Q089:
1. DVWA up; security=medium set (DVWA Security → Medium).
2. uploads dir writable (as Q073).
3. Burp proxy configured.

Report back: "Lab ready for Q089 — DVWA security=medium, uploads writable, Burp ready".
```
