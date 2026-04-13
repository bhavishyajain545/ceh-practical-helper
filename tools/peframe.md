# peframe — static PE analysis

> **One-command malware triage for Windows PE files.** Sections, imports, APIs, URLs, strings, YARA-style indicators.

**Install check:** `peframe --version` (pip install peframe)

---

## 🎯 Cheat-flow

```bash
peframe sample.exe                     # default report
peframe -j sample.exe                  # JSON (scriptable)
peframe -s sample.exe                  # strings only
```

---

## 🔑 Common flags

| Flag | Meaning |
|---|---|
| `-j` / `--json` | JSON output |
| `-s` / `--strings` | Dump strings |
| `-i` / `--import` | Imports |

---

## 📋 What peframe shows

| Section | Why it matters |
|---|---|
| **File info** | MD5/SHA-1/SHA-256, size, type |
| **Hash / fuzzy** | imphash, ssdeep |
| **Sections** | `.text .data .rsrc` — high entropy = packed |
| **Imports / APIs** | `VirtualAllocEx`, `WriteProcessMemory`, `CreateRemoteThread` = injector |
| **URLs / IPs** | C2 leakage |
| **Suspicious APIs** | Anti-debug (`IsDebuggerPresent`), keylog (`GetAsyncKeyState`), crypto |
| **Packer** | UPX, ASPack, Themida, VMProtect |
| **Anti-VM / anti-debug** | Common strings |
| **YARA matches** | Bundled rules |

Typical finding workflow:
1. Check **packer** → unpack with UPX if so.
2. Look at **imports** → infer behavior.
3. Grep **strings/URLs** for C2.
4. Pivot hash to [virustotal](virustotal.md).

---

## ⚠️ Gotchas

- Packed samples hide most imports — unpack first (`upx -d`).
- peframe is static — it won't catch runtime-decoded strings. For those use [strings](strings.md) on a memory dump or run dynamically in a sandbox.
- Project activity is low; pip version may lag — GitHub master is newer.

---

## 🔗 Related

- [peview](peview.md) · [strings](strings.md) · [yara](yara.md) · [virustotal](virustotal.md) · [file](file.md)
