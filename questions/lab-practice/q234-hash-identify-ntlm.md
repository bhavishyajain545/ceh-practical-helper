# Q234 — Identify NTLM Hash

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography / Hash Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hashid` |
| **Time budget** | 3–5 min |

---

## 📝 Question

"Identify: `a4f49c406510bdcab6824ee7c30fd852`. This is a Windows password hash. What specific type is it? What is the hashcat mode?"

---

## 🎯 Flag Format

```
type=<hash_type>; hashcat_mode=<number>
```

---

## 💡 Hints

**Hint 1**

32 hex chars but it's NOT MD5 — context matters (Windows password hash).

**Hint 2**

NTLM hashes are 32 hex characters. Hashcat mode = 1000.

---

## ✅ Solution

```bash
hashid 'a4f49c406510bdcab6824ee7c30fd852' -m
# Shows MD4/MD5/NTLM — context (Windows) confirms NTLM
```

**Answer:** `type=ntlm; hashcat_mode=1000`

---

## 🤖 Claude Setup Prompt

No setup needed.

Report back: "Lab ready for Q234"
