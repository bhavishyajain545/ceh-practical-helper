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

## ⚠️ Gotchas

- peview is read-only — use **CFF Explorer** or **PE-bear** to modify.
- For CLI-scriptable output use [peframe](peframe.md) or `pefile` (python).
- Timestamps are attacker-controlled; don't trust them alone.

---

## 🔗 Related

- [peframe](peframe.md) · [strings](strings.md) · [yara](yara.md) · [file](file.md)
