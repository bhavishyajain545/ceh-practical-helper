# Q038 — PostgreSQL Default Creds

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟡 Medium |
| **Tools** | `psql` |
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
Pre-requisites for Q038:
1. Verify Metasploitable2 (192.168.52.129) up; postgres running: ssh msfadmin@192.168.52.129 → sudo service postgresql status (start if down).
2. Confirm postgres configured to accept remote auth: /etc/postgresql/*/main/pg_hba.conf should have host all all 0.0.0.0/0 md5 (Metasploitable2 default).
3. From Parrot: nmap -p 5432 192.168.52.129 — open. Install psql client: sudo apt install postgresql-client.

Report back: "Lab ready for Q038 — Postgres 5432 open on 192.168.52.129, creds postgres/postgres".
```
