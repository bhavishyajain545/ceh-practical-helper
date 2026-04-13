# olevba — extract VBA macros from Office docs

> **Part of oletools.** Pulls VBA macro code out of `.doc/.docm/.xls/.xlsm/.ppt/.pptm` and flags suspicious constructs.

**Install check:** `olevba --version` (`pip install oletools`)

---

## 🎯 Cheat-flow

```bash
olevba suspicious.docm                # full analysis
olevba -c suspicious.docm             # only code
olevba --decode suspicious.docm       # auto-decode obfuscated strings
olevba --reveal suspicious.docm       # show decoded results inline
olevba -a suspicious.docm             # show analysis only (no code)
olevba --deobf suspicious.docm        # deobfuscate (VBA stomping, hex, chr)
```

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-c` | Display only VBA source code |
| `-a` | Analysis results only |
| `--decode` | Decode obfuscated strings |
| `--reveal` | Show decoded version alongside original |
| `--deobf` | Deep deobfuscation pass |
| `-r` | Recursive on a directory |
| `-z <pwd>` | Password for encrypted zip containing sample |
| `--json` | JSON output |

---

## 📋 What olevba flags

Indicator column meanings:
- **AutoExec** — runs on open (`Document_Open`, `AutoOpen`, `Workbook_Open`)
- **Suspicious** — `Shell`, `CreateObject`, `Kernel32`, `VirtualAlloc`, `Environ`, `WinHttp`, base64
- **IOC** — URLs, IPs, filenames
- **Hex String / Base64 / Dridex** — encoded blobs
- **VBA Stomp** — source removed, only p-code left

Typical doc-borne macro output:
```
|AutoExec  |AutoOpen        |Runs when document opens|
|Suspicious|Shell           |Executes commands       |
|Suspicious|CreateObject    |Creates OLE object      |
|IOC       |hxxp://evil.tld |URL                     |
```

---

## 📋 Recipes

```bash
# 1. Triage a phishing attachment
olevba --decode invoice.docm

# 2. Bulk scan a folder
olevba -r samples/

# 3. JSON → pipe to jq
olevba --json sample.docm | jq '.analysis[] | select(.type=="IOC")'

# 4. Companion tools in oletools
oleid sample.docm        # quick doc characteristics
oledump.py sample.docm   # Didier Stevens alt
rtfobj sample.rtf        # RTF objects
```

---

## ⚠️ Gotchas

- Encrypted Office files need the password first — olevba can't decrypt.
- **VBA stomping** replaces the source with innocuous code while the real p-code runs — olevba flags it; use `pcode2code` to recover.
- Newer `.xlsm` with XLM 4.0 macros → use `oledump` or `XLMMacroDeobfuscator` (olevba only partially handles XLM).
- Always sandbox — even reading the file is *usually* safe, but opening in Word is not.

---

## 🔗 Related

- [pdfid](pdfid.md) · [peframe](peframe.md) · [yara](yara.md) · [virustotal](virustotal.md)
