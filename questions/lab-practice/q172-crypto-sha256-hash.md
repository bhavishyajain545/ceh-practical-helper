# Q172 — Generate and Compare SHA-256 Hash

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography |
| **Difficulty** | 🟢 Easy |
| **Tools** | `sha256sum` |
| **Time budget** | 3–5 min |

---

## 📝 Question

"A file `important.txt` has been provided. Generate its SHA-256 hash and compare it with the expected hash `<given_hash>`. Report whether the file integrity is intact."

---

## 🎯 Flag Format

```
sha256=<hash>; integrity=<valid|invalid>
```

Example: `sha256=abc123...; integrity=valid`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

`sha256sum important.txt`

**Hint 2**

Compare output with given hash — match ho toh `valid`, nahi toh `invalid`.

---

## ✅ Solution

```bash
sha256sum important.txt
```

**Answer:** `sha256=<computed_hash>; integrity=valid`

---

## 🤖 Claude Setup Prompt

```bash
echo "This is an important document for CEH exam." > important.txt
sha256sum important.txt > expected_hash.txt
```

Report back: "Lab ready for Q172 — important.txt and expected hash ready"
