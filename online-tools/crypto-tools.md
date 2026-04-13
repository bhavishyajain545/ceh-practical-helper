# 🔓 Crypto Tools (Online)

> Cipher decrypt, encoding/decoding, RSA factoring, classic ciphers — sab browser mein.

## 🥇 The one tool to rule them all: CyberChef

**URL:** https://gchq.github.io/CyberChef/

**Why:** GCHQ ne banaya hai. **150+ operations**. Drag-and-drop "recipes". Encoding, encryption, hashing, decompression, parsing — sab kuch.

**Kaise use karu (universal flow):**
1. URL khol ke open
2. Right side se "Operations" search karo (e.g. "Base64", "AES Decrypt", "ROT13")
3. Drag karo middle "Recipe" panel mein
4. Left "Input" mein cipher/data paste karo
5. Right "Output" mein result instantly aata hai

**Common CEH recipes:**

| Question type | CyberChef recipe |
|---|---|
| Base64 decode | `From Base64` |
| Hex decode | `From Hex` |
| URL decode | `URL Decode` |
| ROT13 / Caesar | `ROT13` ya `Rotate left` (for shift) |
| AES decrypt | `AES Decrypt` (mode CBC/ECB, key, IV) |
| XOR with key | `XOR` |
| Vigenère | `Vigenère Decode` (with key) |
| Magic auto-detect | `Magic` operation — tries everything! |
| File from base64 | `From Base64` → `Save output to file` |
| Decompress | `Gunzip`, `Inflate`, `Bzip2 Decompress` |

**Magic mode:** Agar pata nahi kya hai, just paste input → search "Magic" → drag. CyberChef khud try karega all known encodings/ciphers.

---

## Classic cipher specific

CEH mein classic ciphers (Caesar, Vigenère, substitution) common hain. CyberChef best hai but backups:

| Tool | URL | Use |
|---|---|---|
| **dCode** | https://www.dcode.fr/en | Sabse comprehensive cipher database. Search "Caesar" / "Vigenère" / etc. |
| **Cryptii** | https://cryptii.com/ | Visual encoder/decoder, similar to CyberChef |
| **Quipqiup** | https://quipqiup.com/ | Auto-solve substitution ciphers (frequency analysis) |
| **Boxentriq** | https://www.boxentriq.com/code-breaking | Cipher identifier + solver |
| **Caesar Cipher Decoder** | https://www.dcode.fr/caesar-cipher | Tries all 26 shifts at once |
| **Vigenère Solver** | https://www.guballa.de/vigenere-solver | Auto-finds key length + plaintext |

**Hinglish tip:** Agar text English-like dikhe but jumbled, classic substitution. Quipqiup pe paste → 5 sec mein solve. Vigenère ke liye Guballa best.

---

## RSA / Asymmetric crypto

| Tool | URL | Use |
|---|---|---|
| **FactorDB** | http://factordb.com/ | RSA modulus N factor karne ke liye. Paste N → check if known factors hain |
| **Alpetron Integer Factor** | https://www.alpertron.com.ar/ECM.HTM | FactorDB ke alternative |
| **dCode RSA** | https://www.dcode.fr/rsa-cipher | Decrypt RSA given p, q, e, c |
| **CyberChef RSA Decrypt** | (in CyberChef) | If you have the private key |

**Flow for "weak RSA" CEH question:**
1. Get N, e, c (ciphertext)
2. FactorDB pe N paste
3. If factored → p, q known
4. dCode RSA mein p, q, e, c daalo → plaintext

---

## Encoding / Decoding (quick)

| Encoding | Online tool | CyberChef op |
|---|---|---|
| Base64 | https://www.base64decode.org/ | From Base64 |
| Base32 | https://emn178.github.io/online-tools/base32_decode.html | From Base32 |
| Hex | https://www.rapidtables.com/convert/number/hex-to-ascii.html | From Hex |
| URL | https://www.urldecoder.org/ | URL Decode |
| HTML entities | https://mothereff.in/html-entities | From HTML Entity |
| Binary | https://www.rapidtables.com/convert/number/binary-to-ascii.html | From Binary |
| Morse | https://morsecode.world/international/translator.html | From Morse Code |

**Pro tip:** CyberChef ek tool mein sab covered hai — ek bookmark, sab kaam.

---

## ⚠️ Gotchas

- **Padding:** Base64 mein `=` padding important hai. Agar missing dikhe, add karo (`=`, `==`)
- **Newlines:** Some tools fail on multi-line input — concat first
- **Charset:** UTF-8 vs ASCII — CyberChef mein "Output as" string vs hex switch karke check karo
- **CyberChef offline:** Tum CyberChef ko download bhi kar sakte ho (single HTML file) — internet kat jaye toh local browser mein chalega
