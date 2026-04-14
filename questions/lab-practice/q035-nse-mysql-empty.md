# Q035 — MySQL Empty Password NSE

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟡 Medium |
| **Tools** | `nmap` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Use `nmap --script mysql-empty-password -p 3306 192.168.52.129`. Report whether root login is available.

---

## 🎯 Flag Format

```
root_empty=<yes|no>
```

Example: `root_empty=yes`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

MySQL on Metasploitable has blank root password.
</details>

<details>
<summary>Hint 2</summary>

Direct test: `mysql -h <ip> -u root`.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nmap --script mysql-empty-password -p 3306 192.168.52.129
```

Reports `root account has empty password`.

**Answer:** `root_empty=yes`

📖 Ref: [playbooks/enumeration-playbook.md](../../playbooks/enumeration-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q035:
1. Verify Metasploitable2 (192.168.52.129) up; mysql running: ssh msfadmin@192.168.52.129 → sudo service mysql status (start if down).
2. From Parrot: nmap -p 3306 192.168.52.129 — open.
3. Confirm blank root: mysql -h 192.168.52.129 -u root (should connect without password).

Report back: "Lab ready for Q035 — MySQL 3306 open on 192.168.52.129, root empty password".
```
