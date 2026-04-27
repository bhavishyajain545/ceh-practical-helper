# Q155 — Detect OpenStego Watermark in Image

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Steganography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `openstego` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"An image `watermarked.png` has a digital watermark embedded using OpenStego. Extract and verify the watermark signature. Report the watermark text."

---

## 🎯 Flag Format

```
watermark=<string>
```

Example: `watermark=COMPANY_SECRET`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

OpenStego mein "Extract Watermark" option use karo.

**Hint 2**

Original image bhi chahiye comparison ke liye — `original.png` use karo.

---

## ✅ Solution

```bash
openstego extractwatermark --stegfile watermarked.png --origfile original.png
```

**Answer:** `watermark=CEH_WATERMARK_2024`

---

## 🤖 Claude Setup Prompt

1. Create original and watermarked images:
```bash
convert -size 300x300 xc:cyan /tmp/original.png
echo "CEH_WATERMARK_2024" > /tmp/wm.txt
openstego embedwatermark --signaturefile /tmp/wm.txt --coverfile /tmp/original.png --stegfile /tmp/watermarked.png
```

Report back: "Lab ready for Q155 — watermarked.png and original.png ready"
