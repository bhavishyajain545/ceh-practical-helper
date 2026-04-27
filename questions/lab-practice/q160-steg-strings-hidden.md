# Q160 — Find Hidden Text in Binary Using Strings

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Steganography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `strings` |
| **Time budget** | 3–5 min |

---

## 📝 Question

"A binary file `mystery.bin` contains readable ASCII strings hidden among binary data. Use the `strings` command to find the flag. The flag starts with `CEH{`."

---

## 🎯 Flag Format

```
flag=<string>
```

Example: `flag=CEH{binary_secrets}`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

`strings mystery.bin` se saare readable strings niklenge.

**Hint 2**

`strings mystery.bin | grep CEH` se flag filter karo.

---

## ✅ Solution

```bash
strings mystery.bin | grep "CEH{"
```

**Answer:** `flag=CEH{strings_are_easy}`

---

## 🤖 Claude Setup Prompt

1. Create binary file with hidden string:
```bash
dd if=/dev/urandom of=/tmp/mystery.bin bs=1024 count=5
echo -n "CEH{strings_are_easy}" >> /tmp/mystery.bin
dd if=/dev/urandom bs=512 count=2 >> /tmp/mystery.bin
cp /tmp/mystery.bin mystery.bin
```

Report back: "Lab ready for Q160 — mystery.bin with hidden string ready"
