# Q022 — smbclient Anonymous Listing

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `enum4linux`, `smbclient`, `rpcclient`, `nmap NSE` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Use `smbclient -L //192.168.52.129 -N` to list shares anonymously. How many disk shares are listed?

---

## 🎯 Flag Format

```
disk_shares=<n>
```

Example: `disk_shares=4`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-L` list shares; `-N` no password.
</details>

<details>
<summary>Hint 2</summary>

Type column = `Disk` rows.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
smbclient -L //192.168.52.129 -N
```

Shows print$, tmp, opt, IPC$ — disk count varies (4).

**Answer:** `disk_shares=4`

📖 Ref: [tools/enum4linux.md](../../tools/enum4linux.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q022".
```
