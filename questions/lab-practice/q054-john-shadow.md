# Q054 — Crack /etc/shadow with john

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `metasploit`, `hydra`, `john`, `hashcat`, `linpeas`, `mimikatz` |
| **Time budget** | 15–25 min |

---

## 📝 Question

From Metasploitable shell, copy `/etc/shadow` and crack with john. Report **msfadmin's password**.

---

## 🎯 Flag Format

```
msfadmin_pwd=<word>
```

Example: `msfadmin_pwd=msfadmin`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`unshadow passwd shadow > combined`, then `john combined`.
</details>

<details>
<summary>Hint 2</summary>

Use `--wordlist=rockyou.txt` for speed.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
unshadow /etc/passwd /etc/shadow > combined.txt
john --wordlist=/usr/share/wordlists/rockyou.txt combined.txt
```

Cracks `msfadmin:msfadmin` quickly.

**Answer:** `msfadmin_pwd=msfadmin`

📖 Ref: [tools/john.md](../../tools/john.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q054".
```
