# Q024 — rpcclient User Enumeration

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `enum4linux`, `smbclient`, `rpcclient`, `nmap NSE` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q024".
```
