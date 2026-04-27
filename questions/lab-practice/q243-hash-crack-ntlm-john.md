# Q243 — Crack NTLM Hash Using John

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography / Hash Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `john` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"Crack NTLM hash `a4f49c406510bdcab6824ee7c30fd852` using John."

---

## 🎯 Flag Format

```
password=<cracked_password>
```

---

## ✅ Solution

```bash
echo "a4f49c406510bdcab6824ee7c30fd852" > ntlm.txt
john --format=nt --wordlist=/usr/share/wordlists/rockyou.txt ntlm.txt
john --show --format=nt ntlm.txt
```

**Answer:** `password=password`

---

## 🤖 Claude Setup Prompt

No setup needed. Report back: "Lab ready for Q243"
