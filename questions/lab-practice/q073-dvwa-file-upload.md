# Q073 — DVWA File Upload PHP Shell

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `curl (or browser)` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Upload a PHP webshell via DVWA upload (low). After upload, what URL do you visit to execute? Provide the path pattern.

---

## 🎯 Flag Format

```
url_pattern=<path>
```

Example: `url_pattern=/dvwa/hackable/uploads/shell.php`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Low security accepts any file.
</details>

<details>
<summary>Hint 2</summary>

Use `pentestmonkey/php-reverse-shell.php` or simple cmd.php.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# Upload shell.php; visit http://192.168.52.129/dvwa/hackable/uploads/shell.php
```

Webshell executes as www-data.

**Answer:** `url_pattern=/dvwa/hackable/uploads/shell.php`

📖 Ref: [playbooks/webapp-playbook.md](../../playbooks/webapp-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q073:
1. DVWA up; security=low; logged in.
2. /var/www/dvwa/hackable/uploads/ must be writable by www-data (Metasploitable2 default: chmod 777).
3. Verify: ssh msfadmin@192.168.52.129 → ls -ld /var/www/dvwa/hackable/uploads (sudo chmod 777 if not writable).

Report back: "Lab ready for Q073 — DVWA uploads dir writable, security=low".
```
