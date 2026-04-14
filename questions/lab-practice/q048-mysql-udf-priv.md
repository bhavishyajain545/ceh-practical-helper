# Q048 — MySQL UDF Root Escalation (Concept)

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🔴 Hard |
| **Tools** | `metasploit`, `hydra`, `john`, `hashcat`, `linpeas`, `mimikatz` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q048".
```
