# Q158 — Detect LSB Steganography in PNG Using zsteg

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Steganography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `zsteg` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"A PNG image `challenge.png` contains hidden data using LSB (Least Significant Bit) encoding. Use zsteg to detect and extract the hidden flag."

---

## 🎯 Flag Format

```
flag=<string>; method=<lsb_method>
```

Example: `flag=LSB_SECRET; method=b1,rgb,lsb`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

`zsteg` sirf PNG/BMP pe kaam karta hai — JPEG pe nahi.

**Hint 2**

`zsteg challenge.png` run karo — ye automatically saare LSB channels check karega.

---

## ✅ Solution

```bash
zsteg challenge.png
```

Look for readable text in the output — it will show which channel/method has the hidden data.

**Answer:** `flag=LSB_HIDDEN_FLAG; method=b1,rgb,lsb`

---

## 🤖 Claude Setup Prompt

1. Create PNG with LSB hidden data using Python:
```python
from PIL import Image
img = Image.new('RGB', (200,200), 'white')
pixels = img.load()
msg = "LSB_HIDDEN_FLAG"
binary = ''.join(format(ord(c),'08b') for c in msg)
for i,bit in enumerate(binary):
    x,y = i%200, i//200
    r,g,b = pixels[x,y]
    pixels[x,y] = (r&~1|int(bit), g, b)
img.save('challenge.png')
```

Report back: "Lab ready for Q158 — challenge.png with LSB hidden data ready"
