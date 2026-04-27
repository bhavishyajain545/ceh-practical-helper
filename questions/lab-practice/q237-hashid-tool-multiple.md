# Q237 — Identify Multiple Hash Types from File

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Cryptography / Hash Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `hashid, hash-identifier` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"A file `mystery_hashes.txt` contains 5 different hashes. Identify each hash type and its corresponding hashcat mode."

---

## 🎯 Flag Format

```
hash1=<type>; hash2=<type>; hash3=<type>; hash4=<type>; hash5=<type>
```

---

## ✅ Solution

```bash
while read hash; do echo "=== $hash ==="; hashid "$hash" -m | head -3; done < mystery_hashes.txt
```

**Answer:** `hash1=md5; hash2=sha1; hash3=sha256; hash4=ntlm; hash5=bcrypt`

---

## 🤖 Claude Setup Prompt

```bash
cat > mystery_hashes.txt << 'HASHES'
5d41402abc4b2a76b9719d911017c592
aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d
2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824
a4f49c406510bdcab6824ee7c30fd852
$2a$10$N9qo8uLOickgx2ZMRZoMyeIjZAgcfl7p92ldGxad68LJZdL17lhWy
HASHES
```

Report back: "Lab ready for Q237 — mystery_hashes.txt with 5 hash types ready"
