# Q167 — Manually Extract LSB Data from PNG Using Python

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Steganography |
| **Difficulty** | 🔴 Hard |
| **Tools** | `python3, PIL/Pillow` |
| **Time budget** | 10–15 min |

---

## 📝 Question

"An image `lsb_image.png` has a message hidden in the LSB of the Red channel. Write a Python script to extract the first 20 characters from the LSB bits. Report the hidden message."

---

## 🎯 Flag Format

```
flag=<string>
```

Example: `flag=LSB_PYTHON_EXTRACT`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

Har pixel ka Red channel ka last bit (LSB) nikalo — 8 bits = 1 character.

**Hint 2**

```python
from PIL import Image
img = Image.open('lsb_image.png')
pixels = list(img.getdata())
bits = [str(p[0] & 1) for p in pixels]
```

---

## ✅ Solution

```python
from PIL import Image
img = Image.open('lsb_image.png')
pixels = list(img.getdata())
bits = ''
for p in pixels[:160]:  # 20 chars * 8 bits
    bits += str(p[0] & 1)
msg = ''
for i in range(0, len(bits), 8):
    msg += chr(int(bits[i:i+8], 2))
print(msg)
```

**Answer:** `flag=CEH_LSB_EXTRACTED!`

---

## 🤖 Claude Setup Prompt

1. Create PNG with LSB encoded message:
```python
from PIL import Image
img = Image.new('RGB', (200,200), (128,128,128))
pixels = img.load()
msg = "CEH_LSB_EXTRACTED!"
binary = ''.join(format(ord(c),'08b') for c in msg)
data = list(img.getdata())
for i, bit in enumerate(binary):
    r,g,b = data[i]
    data[i] = ((r & ~1) | int(bit), g, b)
img2 = Image.new('RGB', (200,200))
img2.putdata(data)
img2.save('lsb_image.png')
```

Report back: "Lab ready for Q167 — lsb_image.png with LSB encoded message ready"
