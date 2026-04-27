# Q164 — Analyze Hex Dump to Find Appended Data

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Steganography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `xxd, hexdump` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"A JPEG file `photo.jpg` has extra data appended after the JPEG end-of-file marker (FF D9). Use hex analysis to find the hidden message appended after the EOF marker."

---

## 🎯 Flag Format

```
flag=<string>
```

Example: `flag=AFTER_EOF_SECRET`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

JPEG files `FF D9` pe end hoti hain — uske baad ka data extra hai.

**Hint 2**

`xxd photo.jpg | grep -A5 "ffd9"` ya `strings photo.jpg | tail` try karo.

---

## ✅ Solution

```bash
xxd photo.jpg | tail -20
# or
strings photo.jpg | tail -5
```

**Answer:** `flag=HEX_DUMP_SECRET`

---

## 🤖 Claude Setup Prompt

1. Create JPEG with appended data:
```bash
convert -size 100x100 xc:white /tmp/photo.jpg
echo "HEX_DUMP_SECRET" >> /tmp/photo.jpg
cp /tmp/photo.jpg photo.jpg
```

Report back: "Lab ready for Q164 — photo.jpg with appended data after EOF marker ready"
