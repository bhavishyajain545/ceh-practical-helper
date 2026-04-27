# Q184 — Convert Hex String to ASCII

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `xxd, python3` |
| **Time budget** | 3–5 min |

---

## 📝 Question

"Convert the following hex string to ASCII and find the flag: `4345487b6865785f746f5f61736369695f666c61677d`"

---

## 🎯 Flag Format

```
flag=<string>
```

Example: `flag=CEH{hex_decoded}`

---

## 💡 Hints

**Hint 1**

`echo "hex_string" | xxd -r -p`

**Hint 2**

Python: `bytes.fromhex("hex_string").decode()`

---

## ✅ Solution

```bash
echo "4345487b6865785f746f5f61736369695f666c61677d" | xxd -r -p
```

**Answer:** `flag=CEH{hex_to_ascii_flag}`

---

## 🤖 Claude Setup Prompt

No setup needed.

Report back: "Lab ready for Q184 — hex to ASCII conversion exercise"
