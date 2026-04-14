# Q028 — LDAP Anonymous Bind

| Field | Value |
|---|---|
| **Target** | `192.168.52.130` (Windows 7) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap` |
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
Pre-requisites for Q028:
1. Verify Win7 (192.168.52.130) up and reachable: ping 192.168.52.130.
2. Win7 is a standalone workgroup — LDAP will NOT be running; expected answer 'closed'.
3. Ensure Win7 firewall allows ping (File & Printer Sharing) so host is not falsely 'down'.

Report back: "Lab ready for Q028 — Win7 reachable; port 389 expected closed".
```
