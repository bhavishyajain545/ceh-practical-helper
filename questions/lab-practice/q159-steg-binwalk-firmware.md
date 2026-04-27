# Q159 — Extract Embedded Files from Image Using Binwalk

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Steganography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `binwalk` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"An image `suspicious.jpg` appears normal but has files appended after the JPEG data. Use binwalk to scan and extract all embedded files. Report what file type was hidden and the flag inside it."

---

## 🎯 Flag Format

```
filetype=<type>; flag=<string>
```

Example: `filetype=zip; flag=BINWALK_FOUND`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

`binwalk suspicious.jpg` se scan karo — embedded files dikhenge.

**Hint 2**

`binwalk -e suspicious.jpg` se extract karo — `_suspicious.jpg.extracted/` folder banega.

---

## ✅ Solution

```bash
binwalk suspicious.jpg
binwalk -e suspicious.jpg
ls _suspicious.jpg.extracted/
cat _suspicious.jpg.extracted/flag.txt
```

**Answer:** `filetype=zip; flag=EMBEDDED_ZIP_FLAG`

---

## 🤖 Claude Setup Prompt

1. Create image with appended ZIP:
```bash
convert -size 200x200 xc:purple /tmp/suspicious.jpg
echo "EMBEDDED_ZIP_FLAG" > /tmp/flag.txt
zip /tmp/hidden.zip /tmp/flag.txt
cat /tmp/suspicious.jpg /tmp/hidden.zip > suspicious.jpg
```

Report back: "Lab ready for Q159 — suspicious.jpg with appended ZIP ready"
