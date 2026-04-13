# yara — pattern matching for malware

> **"Grep on steroids" for binaries.** Write rules once, scan files / processes / memory for matches.

**Install check:** `yara --version`

---

## 🎯 Cheat-flow

```bash
yara rules.yar sample.exe             # scan one file
yara -r rules.yar samples/            # recurse
yara -s rules.yar sample.exe          # show matching strings
yara -m rules.yar sample.exe          # show metadata
yara -w rules.yar sample.exe          # suppress warnings
yara rules.yar --pid 1234             # scan a running process
```

---

## 📝 Rule anatomy

```yara
rule Suspicious_Shell_VBA
{
    meta:
        author = "me"
        description = "Detects VBA Shell() calls"
        date = "2025-01-01"

    strings:
        $s1 = "Shell(" nocase ascii wide
        $s2 = "CreateObject" nocase
        $hex = { 4D 5A 90 00 }                    // MZ header
        $re = /https?:\/\/[a-z0-9.\-]+\.top/     // regex

    condition:
        uint16(0) == 0x5A4D and
        2 of ($s*) and
        filesize < 5MB
}
```

String modifiers: `nocase`, `wide` (UTF-16), `ascii`, `fullword`, `base64`, `xor`.

---

## 🔑 Key flags

| Flag | Meaning |
|---|---|
| `-r` | Recursive directory scan |
| `-s` | Print matching strings |
| `-m` | Print meta |
| `-g` | Print tags |
| `-w` | Suppress warnings |
| `-c` | Print count of matches |
| `-t <tag>` | Only rules with tag |
| `-n` | Only print non-matching |
| `-p <N>` | N threads |
| `-d <name=value>` | Define external variable |
| `--pid <PID>` | Scan process memory |

---

## 📋 Recipes

```bash
# 1. Quick scan with community rules
git clone https://github.com/Yara-Rules/rules && \
yara -r rules/index.yar sample.exe

# 2. Scan a whole directory and print only filenames
yara -r rules.yar samples/ | cut -d' ' -f2 | sort -u

# 3. Debug a rule
yara -s -m rules.yar sample.exe

# 4. Compile rules for reuse
yarac rules.yar rules.yarc
yara rules.yarc sample.exe
```

---

## ⚠️ Gotchas

- **`filesize`** inside a rule speeds things up drastically — use it.
- Regex strings are slower than plain/hex — prefer literals.
- `wide` catches UTF-16 (Windows) strings; add it everywhere.
- Missing modules? `-m hash` / `pe` / `elf` require yara built with those modules.
- For memory scans you may need root and `--pid`.

---

## 🔗 Related

- [peframe](peframe.md) · [strings](strings.md) · [virustotal](virustotal.md) · [olevba](olevba.md)
