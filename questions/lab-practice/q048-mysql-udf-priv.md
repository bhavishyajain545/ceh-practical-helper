# Q048 — MySQL UDF Root Escalation (Concept)

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🔴 Hard |
| **Tools** | `metasploit` |
| **Time budget** | 15–25 min |

---

## 📝 Question

On Metasploitable MySQL (root, blank password), describe the module to load a UDF for command exec. Provide the metasploit module path.

---

## 🎯 Flag Format

```
module=<path>
```

Example: `module=exploit/multi/mysql/mysql_udf_payload`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

The module uses the UDF (User Defined Function) technique to load a shared library via MySQL's auth and execute commands as the mysql user.
</details>

<details>
<summary>Hint 2</summary>

For lab, simpler: `mysql -uroot -h 192.168.52.129` then `\! id` runs locally only.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
msfconsole -q
use exploit/multi/mysql/mysql_udf_payload
```

Module uploads a UDF `.so` into MySQL's plugin path via `SELECT ... INTO DUMPFILE`, then invokes the UDF to execute the payload — yields a shell as the `mysql` user.

**Answer:** `module=exploit/multi/mysql/mysql_udf_payload`

📖 Ref: [playbooks/system-hacking-playbook.md](../../playbooks/system-hacking-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q048:
1. Verify Metasploitable2 (192.168.52.129) up; mysql running with blank root (sudo service mysql status; mysql -h 192.168.52.129 -u root connects).
2. From Parrot: nmap -p 3306 192.168.52.129 — open.
3. msfconsole ready on Parrot.

Report back: "Lab ready for Q048 — MySQL 3306 blank-root on 192.168.52.129".
```
