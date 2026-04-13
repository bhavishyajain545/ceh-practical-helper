# nuclei — template-driven vulnerability scanner

> **YAML templates, fast parallel engine.** Thousands of community-maintained checks for CVEs, exposures, misconfigs.

**Install check:** `nuclei -version`

---

## 🎯 Cheat-flow

```bash
nuclei -u https://target.com                          # single URL, all templates
nuclei -l urls.txt                                    # list
nuclei -u target.com -t cves/                         # only CVE templates
nuclei -u target.com -t exposures/                    # exposures
nuclei -u target.com -severity critical,high          # filter
nuclei -u target.com -tags wordpress                  # tag filter
nuclei -u target.com -id CVE-2021-41773               # one template id
nuclei -u target.com -as                              # automatic scan
nuclei -u target.com -proxy http://127.0.0.1:8080     # through Burp
nuclei -u target.com -o findings.txt -j -je jsonl     # JSON output
nuclei -update-templates                              # refresh templates
```

---

## 🔑 Common flags

| Flag | Meaning |
|---|---|
| `-u <url>` | Single target |
| `-l <file>` | List of targets |
| `-t <path>` | Template path / directory |
| `-tags <tags>` | Filter by tag (e.g. `wordpress,rce`) |
| `-id <id>` | Run one template |
| `-severity <list>` | `info,low,medium,high,critical` |
| `-c <N>` | Concurrency |
| `-rl <N>` | Rate limit (req/s) |
| `-H "Name: val"` | Custom header |
| `-proxy <url>` | Proxy |
| `-o <file>` | Output |
| `-j` / `-je jsonl` | JSON / JSONL |
| `-stats` | Show progress stats |
| `-as` | Auto-scan (tech detect → run relevant templates) |
| `-update-templates` | Pull latest |

---

## 📂 Template categories

Templates live at `~/nuclei-templates/`:

```
cves/              dns/            exposures/        exposed-panels/
file/              fuzzing/        headless/         http/
misconfiguration/  network/        ssl/              takeovers/
tokens/            vulnerabilities/workflows/
```

Pick categories with `-t`, e.g. `-t exposures/tokens/`.

---

## 📋 Recipes

```bash
# 1. Critical-only scan
nuclei -l urls.txt -severity critical -o crit.txt

# 2. WordPress-focused
nuclei -u target.com -tags wordpress -o wp.txt

# 3. Exposed config files / tokens
nuclei -u target.com -t exposures/ -o leak.txt

# 4. Auto technology + relevant templates
nuclei -u target.com -as

# 5. Through Burp for review
nuclei -u target.com -proxy http://127.0.0.1:8080 -severity high,critical
```

---

## ⚠️ Gotchas

- **Update templates** before every engagement: `nuclei -update-templates`.
- Running everything is slow and noisy — use `-severity` / `-tags` to scope.
- Some templates need headless (`-headless`) — require Chromium.
- Custom templates go in `~/nuclei-templates/` or passed with `-t`.
- Respect `-rl` — easy to DoS fragile targets.

---

## 🔗 Related

- [nikto](nikto.md) · [zap](zap.md) · [whatweb](whatweb.md) · [sqlmap](sqlmap.md) · [burpsuite](burpsuite.md)
