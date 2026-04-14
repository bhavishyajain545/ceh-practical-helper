# Q038 — PostgreSQL Default Creds

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟡 Medium |
| **Tools** | `enum4linux`, `smbclient`, `rpcclient`, `nmap NSE` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Connect to Metasploitable PostgreSQL on port 5432 with user `postgres` / password `postgres`. Report the **server version** via `SELECT version();`.

---

## 🎯 Flag Format

```
pg_major=<n>
```

Example: `pg_major=8`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`psql -h 192.168.52.129 -U postgres` (password: postgres).
</details>

<details>
<summary>Hint 2</summary>

`SELECT version();`
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
psql -h 192.168.52.129 -U postgres -c 'SELECT version();'
```

Returns PostgreSQL 8.x.

**Answer:** `pg_major=8`

📖 Ref: [playbooks/enumeration-playbook.md](../../playbooks/enumeration-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q038".
```
