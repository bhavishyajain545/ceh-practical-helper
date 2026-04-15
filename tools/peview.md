# peview — visual PE structure browser

> **Windows GUI for walking the PE header.** Headers, sections, imports, exports, resources — click-through.

**Launch:** `peview.exe` (Windows; run under wine on Parrot: `wine peview.exe sample.exe`)

---

## 🎯 What you see in the left tree

```
IMAGE_DOS_HEADER
IMAGE_NT_HEADERS
  ├─ FILE_HEADER            ← machine, timestamp, #sections
  └─ OPTIONAL_HEADER        ← entry point, image base, subsystem
SECTION_HEADERS             ← .text .rdata .data .rsrc .reloc
IMPORT_TABLE                ← DLLs + functions the binary calls
EXPORT_TABLE
RESOURCE_TABLE              ← icons, manifests, embedded files
DEBUG / BASE_RELOC / TLS
```

## 🎯 What to check quickly

| Look at | Finding |
|---|---|
| `FILE_HEADER.TimeDateStamp` | Compile time (can be faked) |
| `OPTIONAL_HEADER.AddressOfEntryPoint` | Unusual entry outside `.text` = packed |
| Section **Raw size vs Virtual size** | Big gap = packer |
| Section **Characteristics** | `.text` writable + executable = suspicious |
| `IMPORT_TABLE` | Few imports = packed; `LoadLibraryA` + `GetProcAddress` only = dynamic resolve |
| `RESOURCE_TABLE` | Embedded PEs, configs, icons |

---

## 🧪 CEH malware-triage recipe

When CEH gives you a suspicious `.exe` / `.dll`, walk this sequence in peview:

1. **FILE_HEADER → TimeDateStamp** — note compile date (even if fake, useful reference).
2. **FILE_HEADER → Machine** — x86 (`0x14c`) or x64 (`0x8664`)?
3. **OPTIONAL_HEADER → Subsystem** — GUI (`2`), Console (`3`), Native (`1`).
4. **OPTIONAL_HEADER → AddressOfEntryPoint** — does it fall inside `.text`? If inside `.UPX0`, `.aspack`, or a custom-named section → **packed**.
5. **SECTION_HEADERS** — look at each section:
   - `Raw size = 0, Virtual size > 0` → UPX/packer signature
   - Writable + Executable characteristics → unpacker stub
   - Weird names (`.UPX0`, `.aspack`, `.mpress`) → packer
6. **IMPORT_TABLE** — count total imports:
   - **<10 imports** + only `LoadLibraryA` / `GetProcAddress` / `VirtualAlloc` → **packed** (real imports resolved at runtime)
   - High-signal API imports to flag: `VirtualAlloc`, `WriteProcessMemory`, `CreateRemoteThread`, `SetWindowsHookEx`, `URLDownloadToFile`, `WinExec`, `ShellExecute`, `CryptEncrypt`, `RegSetValueEx` (persistence)
7. **RESOURCE_TABLE** — suspicious if contains embedded PE (check resource size + binary magic `MZ`).
8. **EXPORT_TABLE** — exported functions = likely a DLL; note names.

**Packer detection shortcut:**
- Sections `.UPX0` / `.UPX1` → UPX (unpack with `upx -d sample.exe`)
- Section `.aspack` → ASPack
- No identifiable named sections + high entropy → generic packer → try `die` (Detect-It-Easy) or `peframe`

---

## ⚠️ Gotchas

- peview is read-only — use **CFF Explorer** or **PE-bear** to modify.
- For CLI-scriptable output use [peframe](peframe.md) or `pefile` (python).
- Timestamps are attacker-controlled; don't trust them alone.

---

## 🔗 Related

- [peframe](peframe.md) · [strings](strings.md) · [yara](yara.md) · [file](file.md)
