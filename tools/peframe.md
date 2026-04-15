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

## 📸 Sample output snippet

```
File name: sample.exe
File size: 184320
Hash MD5:  44d88612fea8a8f36de82e1278abb02f
Hash SHA1: 3395856ce81f2b7382dee72602f798b642f14140
Imphash:   8a3e53bc6e4fa56881a6148c0a4c6d2e
Packer:    UPX 3.91 (Markus Oberhumer, Laszlo Molnar & John Reiser)
Sections:  .UPX0 entropy=0.0  .UPX1 entropy=7.9 (PACKED)
Suspicious APIs (8):
  VirtualAllocEx, WriteProcessMemory, CreateRemoteThread,
  GetAsyncKeyState, IsDebuggerPresent, WinExec,
  URLDownloadToFileA, ShellExecuteExA
URLs:      http://malicious-c2.example[.]com/gate.php
Strings suspicious: cmd.exe /c, powershell -enc, regsvr32
YARA matches: Keylogger_Generic, UPX_Packer
```

---

## 🧪 CEH malware-triage recipe

1. **Run peframe** on the sample: `peframe sample.exe`.
2. **Check packer** — if `UPX` shown, unpack first: `upx -d sample.exe -o unpacked.exe`, then re-run peframe on `unpacked.exe`.
3. **Read "Suspicious APIs" list** — match against categories:
   - Process injection: `VirtualAllocEx`, `WriteProcessMemory`, `CreateRemoteThread`, `NtCreateThreadEx`
   - Keylogging: `GetAsyncKeyState`, `SetWindowsHookEx`, `GetKeyboardState`
   - Network / C2: `InternetOpen`, `InternetConnect`, `HttpSendRequest`, `URLDownloadToFile`, `WSAStartup`, `connect`
   - Anti-debug / anti-VM: `IsDebuggerPresent`, `CheckRemoteDebuggerPresent`, `GetTickCount`, `NtQueryInformationProcess`
   - Persistence: `RegCreateKey`, `RegSetValueEx`, `CreateService`, `SetWindowsHookEx`
   - Execution: `WinExec`, `ShellExecute`, `CreateProcess`, `system`
4. **Grep URLs / IPs** from output — these are the C2 indicators to submit.
5. **Check YARA matches** — built-in ruleset flags common families.
6. **Pivot hashes** (MD5/SHA1/imphash) to [VirusTotal](virustotal.md) for family attribution.

---

## 🧰 JSON output + jq

Scriptable extraction for bulk triage:

```bash
peframe -j sample.exe > out.json

# Pull just the suspicious APIs
jq '.peinfo.features.apialert' out.json

# Extract all URLs
jq '.peinfo.features.url' out.json

# Check packer identification
jq '.peinfo.features.packer' out.json

# Compare imphash across samples (cluster similar malware)
jq -r '.imphash' *.json | sort | uniq -c | sort -rn
```

---

## 🚩 High-signal API imports to flag (quick-reference)

| API | Likely behavior |
|---|---|
| `VirtualAllocEx` + `WriteProcessMemory` + `CreateRemoteThread` | **Classic process injection** |
| `SetWindowsHookEx` | **Keylogger** or global hook |
| `GetAsyncKeyState` | **Keystroke capture** |
| `URLDownloadToFileA` | **Downloader / dropper** |
| `InternetOpen` + `InternetConnect` + `HttpSendRequest` | **HTTP C2** |
| `CryptEncrypt` / `CryptGenKey` | **Ransomware / encryptor** |
| `RegSetValueEx` under `Run` key | **Persistence** |
| `IsDebuggerPresent` / `OutputDebugString` tricks | **Anti-debug** |
| `NtQueryInformationProcess` (ProcessDebugPort) | **Advanced anti-debug** |
| `CreateToolhelp32Snapshot` + `Process32First` | **Process enumeration** (before injection) |

---

## ⚠️ Gotchas

- Packed samples hide most imports — unpack first (`upx -d`).
- peframe is static — it won't catch runtime-decoded strings. For those use [strings](strings.md) on a memory dump or run dynamically in a sandbox.
- Project activity is low; pip version may lag — GitHub master is newer.

---

## 🔗 Related

- [peview](peview.md) · [strings](strings.md) · [yara](yara.md) · [virustotal](virustotal.md) · [file](file.md)
