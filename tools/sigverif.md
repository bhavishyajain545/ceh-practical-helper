# sigverif — Windows File Signature Verification

> **Built-in Windows tool to list unsigned system files.** CEH theory asks "which tool verifies driver/system file signatures?" — answer: sigverif.

**Launch (Windows):** `Start → Run → sigverif` or `sigverif.exe`

---

## 🎯 Usage

1. Run `sigverif`.
2. Click **Start**.
3. It scans system files (default: `%WINDIR%\system32` and drivers).
4. Produces a list of **unsigned files** → logged to `%USERPROFILE%\Documents\sigverif.txt` (or `%WINDIR%\sigverif.txt` depending on version).

Advanced dialog lets you change the search path or log location.

---

## 🧪 Equivalent / better CLI tools

| Tool | Usage |
|---|---|
| `sigcheck.exe` (Sysinternals) | `sigcheck -u -e C:\Windows\System32` — list unsigned executables |
| `signtool verify /pa file.exe` | Verify Authenticode signature |
| PowerShell | `Get-AuthenticodeSignature file.exe` |

**sigcheck** is far more useful for real-world triage:
```cmd
sigcheck -u -e -s C:\              :: recursive, only unsigned EXEs
sigcheck -a -h file.exe            :: all info + hashes
sigcheck -vt file.exe              :: VirusTotal lookup
```

---

## ⚠️ Gotchas

- sigverif only checks against Microsoft's catalogue — a signed-by-someone-else file still counts as "unsigned" in some configs.
- Scan is slow on large drives.
- Not present on Windows Server Core.

---

## 🔗 Related

- [virustotal](virustotal.md) · [md5sum](md5sum.md) · [peframe](peframe.md)
