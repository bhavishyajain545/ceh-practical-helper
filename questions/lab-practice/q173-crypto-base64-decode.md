# Q173 — Decode Base64 Encoded String

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `base64` |
| **Time budget** | 2–3 min |

---

## 📝 Question

"Decode the following Base64 string and report the hidden message: `Q0VIe2Jhc2U2NF9kZWNvZGVkX3N1Y2Nlc3NmdWxseX0=`"

---

## 🎯 Flag Format

```
flag=<decoded_string>
```

Example: `flag=CEH{decoded_message}`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

`echo "<base64_string>" | base64 -d`

**Hint 2**

`-d` flag decode karta hai, `-e` encode karta hai.

---

## ✅ Solution

```bash
echo "Q0VIe2Jhc2U2NF9kZWNvZGVkX3N1Y2Nlc3NmdWxseX0=" | base64 -d
```

**Answer:** `flag=CEH{base64_decoded_successfully}`

---

## 🤖 Claude Setup Prompt

No setup needed — built-in command.

Report back: "Lab ready for Q173 — base64 decode exercise"
