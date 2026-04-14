# Q028 — LDAP Anonymous Bind

| Field | Value |
|---|---|
| **Target** | `192.168.52.130` (Windows 7) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `enum4linux`, `smbclient`, `rpcclient`, `nmap NSE` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Run `ldapsearch -x -h 192.168.52.130 -s base namingcontexts` on Win7 (if AD/LDAP enabled). Or confirm port 389 state.

---

## 🎯 Flag Format

```
ldap389=<state>
```

Example: `ldap389=closed`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`nmap -p 389 <ip>` first.
</details>

<details>
<summary>Hint 2</summary>

Standalone Win7 (non-AD) → LDAP closed.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nmap -p 389 192.168.52.130
```

Port 389 closed on standalone Win7.

**Answer:** `ldap389=closed`

📖 Ref: [tools/ldapsearch.md](../../tools/ldapsearch.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q028".
```
