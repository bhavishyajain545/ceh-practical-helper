# Q189 — Multi-Layer Decode: Base64 → Hex → ROT13

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography |
| **Difficulty** | 🔴 Hard |
| **Tools** | `base64, xxd, tr` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"A flag has been encoded in 3 layers. The encoded string is: `NEE0NTY4NjU3ODVmNjQ2NTYzNmY2NDY1NjQ=`. Layer 1: Base64 → Layer 2: Hex → Layer 3: ROT13. Decode all layers to find the flag."

---

## 🎯 Flag Format

```
flag=<string>
```

Example: `flag=CEH{multi_decoded}`

---

## 💡 Hints

**Hint 1**

Step 1: Base64 decode → Step 2: Hex to ASCII → Step 3: ROT13 decode

**Hint 2**

```bash
echo "encoded" | base64 -d | xxd -r -p | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

---

## ✅ Solution

```bash
echo "NEE0NTY4NjU3ODVmNjQ2NTYzNmY2NDY1NjQ=" | base64 -d | xxd -r -p | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

**Answer:** `flag=hex_decoded`

---

## 🤖 Claude Setup Prompt

No setup needed — pure decoding exercise.

Report back: "Lab ready for Q189 — multi-layer decode challenge"
