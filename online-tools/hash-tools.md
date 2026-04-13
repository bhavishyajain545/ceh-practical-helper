# 🔐 Hash Tools (Online)

> Hash crack karna, identify karna, ya generate karna — sab browser mein.

## Hash identify karne ke liye

| Tool | URL | Kab use karu | Kaise use karu |
|---|---|---|---|
| **hashes.com Identify** | https://hashes.com/en/tools/hash_identifier | Jab hashid bhi confused ho | Hash paste → "Identify" click. Multiple matches dega with confidence. |
| **TunnelsUp Hash Analyzer** | https://www.tunnelsup.com/hash-analyzer/ | Length-based identification | Hash paste → length + possible types dikhayega |
| **OnlineHashCrack ID** | https://www.onlinehashcrack.com/hash-identification.php | Backup option | Same flow |

**Hinglish tip:** Pehle `hashid <hash>` terminal mein chala. Agar 3+ types aaye aur confused ho, toh in tools mein paste karo — visual comparison better hota hai.

---

## Hash crack karne ke liye (lookup-based)

Ye sites pre-computed rainbow tables use karte hain. Agar hash already cracked hai global database mein, instant answer milta hai. Common hashes (MD5/SHA1 of dictionary words) ke liye **sabse fast method** hai.

| Tool | URL | Hash types | Notes |
|---|---|---|---|
| **CrackStation** | https://crackstation.net/ | MD5, SHA1, SHA256, NTLM, MySQL, etc. | **Best free option.** Paste hash, Captcha, click Crack. Instant. |
| **Hashes.com Decrypt** | https://hashes.com/en/decrypt/hash | All major | Multi-hash batch supported. Free. |
| **MD5 Decrypter** | https://md5decrypt.net/ | MD5, SHA1, NTLM | Older but works |
| **OnlineHashCrack** | https://www.onlinehashcrack.com/ | WPA, NTLM, MD5, etc. | Some paid features |
| **HashKiller** | https://hashkiller.io/listmanager | NTLM, MD5 | Big NTLM database |

**Use case:** Exam mein NTLM hash mila → CrackStation pe paste → 80% chance instant plaintext. **John/hashcat se faster** for common passwords.

---

## Hash generate karne ke liye

Kab? Jab tumhe ek password ka hash banana ho (e.g. SQLi mein admin user insert karna ho).

| Tool | URL | Notes |
|---|---|---|
| **CyberChef** | https://gchq.github.io/CyberChef/ | Search "MD5" / "SHA1" / "NTLM" → drag → input → output |
| **md5hashgenerator.com** | https://www.md5hashgenerator.com/ | Single-purpose MD5 |
| **PasswordsGenerator** | https://passwordsgenerator.net/sha256-hash-generator/ | SHA family |

---

## NTLM specific

NTLM hashes Windows passwords ke liye sabse common hain CEH practical mein.

```
Format: aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0
        ^^^ LM hash (usually blank) ^^^^^^ NT hash (the one you crack) ^^^
```

- **CrackStation** lets you paste the NT hash part directly.
- **Hashcat mode 1000** for the NT hash.
- **Combo cracked dump dictionaries** at https://hashes.com/en/decrypt/hash work for huge wordlist matching.

---

## WPA / WPA2 handshake online

| Tool | URL | Notes |
|---|---|---|
| **OnlineHashCrack WPA** | https://www.onlinehashcrack.com/ | Upload .cap/.hccapx — paid for actual crack but free check |
| **GPUHash.me** | https://gpuhash.me/ | WPA + many other types |

**Reality check:** WPA crack online slow + paid. CEH practical mein local hashcat (mode 22000) try karo pehle.

---

## ⚠️ Gotchas

- **Privacy:** ye public sites hain, exam target hash paste safe hai (nobody cares about a CTF box hash) but real-world target hashes never upload karna
- **Captcha:** CrackStation mein har query pe captcha hai
- **Hash format matters:** trailing newline / extra whitespace cause "not found" — paste karne se pehle trim karo
