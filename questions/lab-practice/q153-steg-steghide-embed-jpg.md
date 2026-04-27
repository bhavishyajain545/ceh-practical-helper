# Q153 — Embed a Secret File into JPEG Using Steghide

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Steganography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `steghide` |
| **Time budget** | 5–8 min |

---

## 📝 Question

"Embed the file `flag.txt` into `cover.jpg` using steghide with passphrase `embedme`. Then verify the embedding was successful by checking steghide info. Report the embedding capacity used."

---

## 🎯 Flag Format

```
embedded=<yes|no>; capacity=<value>
```

Example: `embedded=yes; capacity=3.2KB`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

`steghide embed -cf cover.jpg -ef flag.txt -p embedme`

**Hint 2**

`steghide info cover.jpg` se embedding details milenge.

---

## ✅ Solution

```bash
steghide embed -cf cover.jpg -ef flag.txt -p embedme
steghide info cover.jpg -p embedme
```

**Answer:** `embedded=yes; capacity=depends_on_image`

---

## 🤖 Claude Setup Prompt

1. Create cover image and flag file:
```bash
convert -size 400x400 xc:red /tmp/cover.jpg
echo "CEH_EMBED_SUCCESS" > /tmp/flag.txt
```
2. Place both files in working directory.

Report back: "Lab ready for Q153 — cover.jpg and flag.txt ready for embedding"
