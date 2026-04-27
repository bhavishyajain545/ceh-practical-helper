# Q156 — Extract Hidden Message from Whitespace Using Snow

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Steganography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `snow` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"A text file `readme.txt` looks normal but contains a hidden message encoded in trailing whitespace using the Snow tool. The password is `icepass`. Extract and report the hidden flag."

---

## 🎯 Flag Format

```
flag=<string>
```

Example: `flag=SNOW_HIDDEN_MSG`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

`snow` tool whitespace steganography ke liye use hota hai — trailing spaces/tabs mein data hide karta hai.

**Hint 2**

`snow -C -p icepass readme.txt` se extract hoga.

---

## ✅ Solution

```bash
snow -C -p icepass readme.txt
```

**Answer:** `flag=WHITESPACE_SECRET`

---

## 🤖 Claude Setup Prompt

1. Create text file with Snow hidden message:
```bash
echo "This is a normal looking text file with some content." > /tmp/readme.txt
snow -C -m "WHITESPACE_SECRET" -p icepass /tmp/readme.txt /tmp/readme_steg.txt
mv /tmp/readme_steg.txt readme.txt
```

Report back: "Lab ready for Q156 — readme.txt with Snow whitespace steganography ready"
