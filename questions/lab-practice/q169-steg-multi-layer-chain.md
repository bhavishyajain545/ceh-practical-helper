# Q169 — Multi-Layer Steganography Chain

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Steganography |
| **Difficulty** | 🔴 Hard |
| **Tools** | `binwalk, steghide, base64` |
| **Time budget** | 15–20 min |

---

## 📝 Question

"An image `layered.jpg` has multiple layers of hidden data. First, binwalk reveals an embedded file. That file is steghide-protected (passphrase: `layer2`). The extracted content is base64 encoded. Decode all layers to find the final flag."

---

## 🎯 Flag Format

```
flag=<string>
```

Example: `flag=MULTI_LAYER_WIN`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

Step 1: `binwalk -e layered.jpg` → Step 2: steghide on extracted file → Step 3: base64 decode

**Hint 2**

Har layer ka output next layer ka input hai — sequential karo.

---

## ✅ Solution

```bash
# Layer 1: Extract embedded files
binwalk -e layered.jpg
# Layer 2: Steghide extract from embedded JPEG
steghide extract -sf _layered.jpg.extracted/inner.jpg -p layer2
# Layer 3: Base64 decode
cat encoded.txt | base64 -d
```

**Answer:** `flag=CEH_MULTILAYER_MASTER`

---

## 🤖 Claude Setup Prompt

1. Create multi-layered steg file:
```bash
# Layer 3: Base64 encode flag
echo -n "CEH_MULTILAYER_MASTER" | base64 > /tmp/encoded.txt
# Layer 2: Embed in inner JPEG
convert -size 200x200 xc:gray /tmp/inner.jpg
steghide embed -cf /tmp/inner.jpg -ef /tmp/encoded.txt -p layer2 -f
# Layer 1: Append inner.jpg to outer JPEG
convert -size 300x300 xc:blue /tmp/layered.jpg
cat /tmp/layered.jpg /tmp/inner.jpg > layered.jpg
```

Report back: "Lab ready for Q169 — layered.jpg with 3-layer steganography chain ready"
