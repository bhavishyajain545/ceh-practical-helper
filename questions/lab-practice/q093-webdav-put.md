# Q093 — Apache WebDAV PUT

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `burpsuite`, `sqlmap`, `gobuster`, `nikto`, `curl` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Test if Metasploitable Apache supports `PUT` via WebDAV. Use curl to attempt upload.

---

## 🎯 Flag Format

```
put_allowed=<yes|no>
```

Example: `put_allowed=no`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`curl -X PUT --data 'test' http://<ip>/test.txt -v`
</details>

<details>
<summary>Hint 2</summary>

Returns 405 Method Not Allowed by default.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
curl -X PUT --data 'test' http://192.168.52.129/test.txt -v
```

405 Method Not Allowed.

**Answer:** `put_allowed=no`

📖 Ref: [playbooks/web-servers-playbook.md](../../playbooks/web-servers-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q093".
```
