# Q083 — SSRF via URL Param (Mutillidae)

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🔴 Hard |
| **Tools** | `burpsuite`, `sqlmap`, `gobuster`, `nikto`, `curl` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Mutillidae's URL fetch page can be abused for SSRF. Provide an internal URL that retrieves Apache server status.

---

## 🎯 Flag Format

```
url=<url>
```

Example: `url=http://127.0.0.1/server-status`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Server-side fetches whatever URL given.
</details>

<details>
<summary>Hint 2</summary>

Try internal-only paths.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# http://192.168.52.129/mutillidae/index.php?page=ssrf.php&url=http://127.0.0.1/server-status
```

Returns localhost-only Apache status.

**Answer:** `url=http://127.0.0.1/server-status`

📖 Ref: [playbooks/webapp-playbook.md](../../playbooks/webapp-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q083".
```
