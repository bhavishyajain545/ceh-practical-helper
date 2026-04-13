# pdfid — PDF object triage

> **Didier Stevens' quick-look for PDFs.** Counts suspicious keywords and object types without rendering the PDF.

**Install check:** `pdfid.py --version` (pdf-parser/pdfid package or pip install peepdf/didier-stevens-suite)

---

## 🎯 Cheat-flow

```bash
pdfid.py sample.pdf                   # count keywords
pdfid.py -e sample.pdf                # include extra keywords
pdfid.py -f sample.pdf                # force scan entire file
pdf-parser.py sample.pdf              # companion — dump objects
pdf-parser.py -o 5 sample.pdf         # show object 5
pdf-parser.py -f -o 5 sample.pdf      # decode filters
```

---

## 🔑 pdfid keyword meanings

| Keyword | Meaning |
|---|---|
| `/JS` `/JavaScript` | Embedded JS — suspicious |
| `/AA` `/OpenAction` | **Auto-action on open** — big red flag |
| `/Launch` | Runs external program |
| `/EmbeddedFile` | File hidden inside PDF |
| `/RichMedia` | Flash/3D (CVE playground) |
| `/URI` | External URL |
| `/AcroForm` `/XFA` | Form — possible XFA exploits |
| `/ObjStm` | Object stream (can hide other objects) |
| `/Encrypt` | PDF is encrypted |
| `obj` / `endobj` | Object count |
| `stream` / `endstream` | Stream count |

Non-zero on `/JS`, `/OpenAction`, `/Launch`, `/EmbeddedFile` → investigate with `pdf-parser`.

---

## 📋 Workflow

```bash
# 1. Triage
pdfid.py invoice.pdf

# 2. If /JS > 0 — find the JS object
pdf-parser.py -s JavaScript invoice.pdf
# note the object number, e.g. 12 0 R

# 3. Dump and decode
pdf-parser.py -o 12 -f invoice.pdf

# 4. If /EmbeddedFile — extract
pdf-parser.py -s EmbeddedFile -f --raw invoice.pdf > embedded.bin
```

---

## ⚠️ Gotchas

- pdfid is a **keyword counter** — false positives happen (benign PDFs can have `/JS`).
- `/ObjStm` hides other keywords — use `-e` or `peepdf` for deeper analysis.
- Use [peepdf](https://github.com/jesparza/peepdf) for interactive exploration (`peepdf -i sample.pdf`).
- Rendering the PDF is risky — always work statically.

---

## 🔗 Related

- [olevba](olevba.md) · [yara](yara.md) · [virustotal](virustotal.md) · [strings](strings.md)
