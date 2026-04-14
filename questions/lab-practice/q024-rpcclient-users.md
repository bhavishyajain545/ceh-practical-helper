# Q024 — rpcclient User Enumeration

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `rpcclient` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Use `rpcclient -U "" -N 192.168.52.129` then `enumdomusers`. List the **count of users** returned.

---

## 🎯 Flag Format

```
user_count=<n>
```

Example: `user_count=33`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

rpcclient interactive shell after null login.
</details>

<details>
<summary>Hint 2</summary>

Use `enumdomusers` and count rid entries.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
rpcclient -U "" -N 192.168.52.129 -c 'enumdomusers'
```

Returns ~33 system + service users.

**Answer:** `user_count=33`

📖 Ref: [tools/enum4linux.md](../../tools/enum4linux.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q024:
1. Verify Metasploitable2 (192.168.52.129) up; samba running (sudo service samba status).
2. From Parrot: nmap -p 139,445 192.168.52.129 — both open.
3. rpcclient is part of samba-client on Parrot: which rpcclient.

Report back: "Lab ready for Q024 — rpcclient ready, port 139/445 open on 192.168.52.129".
```
