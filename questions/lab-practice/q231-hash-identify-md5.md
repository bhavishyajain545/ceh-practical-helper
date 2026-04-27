# Q231 — Identify MD5 Hash

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography / Hash Analysis |
| **Difficulty** | 🟢 Easy |
| **Tools** | `hash-identifier, hashid` |
| **Time budget** | 3–5 min |

---

## 📝 Question

"Identify the hash type of: `5d41402abc4b2a76b9719d911017c592`. Use hash-identifier or hashid tool. Also determine the original plaintext (it's a common word)."

---

## 🎯 Flag Format

```
type=<hash_type>; plaintext=<word>
```

---

## 💡 Hints

**Hint 1**

32 characters hex = likely MD5.

**Hint 2**

`hashid '5d41402abc4b2a76b9719d911017c592'` ya `hash-identifier` mein paste karo.

---

## ✅ Solution

```bash
hashid '5d41402abc4b2a76b9719d911017c592'
echo -n "hello" | md5sum  # verify
```

**Answer:** `type=md5; plaintext=hello`

---

## 🤖 Claude Setup Prompt

No setup needed.

Report back: "Lab ready for Q231 — MD5 hash identification exercise"
