# Q188 — XOR Decrypt a Message

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `python3` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"A message has been XOR encrypted with key `K`. The encrypted hex is `0800031a0a061e17`. XOR decrypt it and find the flag."

---

## 🎯 Flag Format

```
flag=<string>
```

Example: `flag=CEH_XOR`

---

## 💡 Hints

**Hint 1**

XOR same key se dobara apply karo toh original message milta hai.

**Hint 2**

```python
encrypted = bytes.fromhex("0800031a0a061e17")
key = ord('K')
decrypted = ''.join(chr(b ^ key) for b in encrypted)
```

---

## ✅ Solution

```python
encrypted = bytes.fromhex("0800031a0a061e17")
key = ord('K')
decrypted = ''.join(chr(b ^ key) for b in encrypted)
print(decrypted)
```

**Answer:** `flag=CEH_XORD`

---

## 🤖 Claude Setup Prompt

No setup needed — Python exercise.

Report back: "Lab ready for Q188 — XOR decryption exercise"
