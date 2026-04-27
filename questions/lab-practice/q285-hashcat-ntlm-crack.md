# Q285 — Crack NTLM Hashes Using Hashcat

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | System Hacking / Password Cracking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hashcat` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"Crack NTLM hashes from a Windows SAM dump using hashcat. NTLM mode is 1000."

---

## 🎯 Flag Format

```
password=<cracked_pass>; mode=<hashcat_mode>
```

---

## ✅ Solution

```bash
echo "a4f49c406510bdcab6824ee7c30fd852" > ntlm.txt
hashcat -m 1000 ntlm.txt /usr/share/wordlists/rockyou.txt
hashcat -m 1000 ntlm.txt --show
```

**Answer:** `password=password; mode=1000`

---

## 🤖 Claude Setup Prompt

No setup needed. Report back: "Lab ready for Q285"
