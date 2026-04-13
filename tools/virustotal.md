# virustotal — multi-engine file/URL/IP lookup

> **The "is this malicious?" lookup.** No API key needed for hash / URL searches via the web UI.

**URL:** https://www.virustotal.com

---

## 🎯 Cheat-flow (no API key)

| You have... | Paste into |
|---|---|
| A file hash (MD5/SHA-1/SHA-256) | `https://www.virustotal.com/gui/search/<hash>` |
| A URL | `https://www.virustotal.com/gui/search/<urlencoded-url>` |
| A domain | `https://www.virustotal.com/gui/domain/<domain>` |
| An IP | `https://www.virustotal.com/gui/ip-address/<ip>` |
| A file | Upload at `/gui/home/upload` (≤ 650 MB) |

Tabs to know:
- **Detection** — AV engine verdicts
- **Details** — hashes, size, metadata, imphash, ssdeep
- **Relations** — dropped files, contacted IPs/URLs, parents
- **Behavior** — sandbox telemetry (registry, network, processes)
- **Community** — analyst comments

---

## 📋 Typical exam workflow

```bash
# 1. Get the hash
sha256sum suspect.exe | awk '{print $1}'

# 2. Open VT in browser
xdg-open "https://www.virustotal.com/gui/search/$(sha256sum suspect.exe | awk '{print $1}')"

# 3. Read the verdict ratio (e.g. 52/70)
# 4. Check "Details → Names" for previous filenames
# 5. "Relations" for C2 IPs
```

---

## 🔑 With an API key (vt-cli)

```bash
pip install vt-py            # python
# or binary: github.com/VirusTotal/vt-cli
vt file <sha256>
vt url  https://evil.tld
vt domain evil.tld
vt ip   1.2.3.4
vt scan file sample.exe
vt download <sha256>         # VT Enterprise only
```

Env: `export VT_APIKEY=...`

---

## ⚠️ Gotchas

- **Don't upload sensitive samples** — uploaded files are shared with VT partners. Prefer **hash lookup first** (sha256sum → web search). Upload only if hash is unknown.
- Free API: 4 req/minute, 500/day.
- Old detections may be stale — click "Reanalyze".
- Low detection ratio ≠ clean (targeted malware is single-digit). High = almost certainly bad.
- URL-encode special characters in URL searches.

---

## 🔗 Related

- [md5sum](md5sum.md) · [sha256sum](md5sum.md) · [yara](yara.md) · [peframe](peframe.md)
