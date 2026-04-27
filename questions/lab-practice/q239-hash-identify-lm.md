# Q239 — Identify LM Hash

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography / Hash Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hashid` |
| **Time budget** | 5–8 min |

---

## 📝 Question

"From a SAM dump: `Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::`. Identify the LM hash and NTLM hash portions. Why is the LM hash `aad3b435b51404ee` repeated?"

---

## 🎯 Flag Format

```
lm_hash=<hash>; ntlm_hash=<hash>; lm_empty=<yes|no>
```

---

## ✅ Solution

Format: `username:RID:LM_HASH:NTLM_HASH:::`
- LM: `aad3b435b51404eeaad3b435b51404ee` — this is the LM hash of an EMPTY password (LM disabled)
- NTLM: `31d6cfe0d16ae931b73c59d7e0c089c0`

**Answer:** `lm_hash=aad3b435b51404eeaad3b435b51404ee; ntlm_hash=31d6cfe0d16ae931b73c59d7e0c089c0; lm_empty=yes`

---

## 🤖 Claude Setup Prompt

No setup needed. Report back: "Lab ready for Q239"
