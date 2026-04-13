# wappalyzer — tech stack fingerprinter

> **Browser extension + CLI for identifying web technologies.** Similar to [whatweb](whatweb.md) but with a richer tech database.

**Install:** Browser extension (Chrome/Firefox) or CLI `npm install -g wappalyzer`.

---

## 🎯 Cheat-flow

**Browser:** click the extension icon while on the target site → pop-up lists frameworks, CMS, analytics, CDN, server, language, etc.

**CLI:**
```bash
wappalyzer https://target.com
wappalyzer https://target.com --pretty            # pretty JSON
wappalyzer https://target.com --recursive         # crawl
wappalyzer https://target.com --max-depth=3
wappalyzer https://target.com --user-agent "..."
wappalyzer https://target.com --proxy http://127.0.0.1:8080
wappalyzer https://target.com --set-cookies "sid=abc"
```

---

## 🔑 Common flags

| Flag | Meaning |
|---|---|
| `--recursive` / `-r` | Crawl links |
| `--max-depth <N>` | Crawl depth |
| `--max-urls <N>` | Cap URLs |
| `--max-wait <ms>` | Per-page wait |
| `--user-agent <str>` | UA |
| `--proxy <url>` | Proxy |
| `--set-headers "K: V"` | Custom header |
| `--pretty` | Pretty print JSON |

---

## 📋 Example output

```json
{
  "urls": { "https://target.com": { "status": 200 } },
  "technologies": [
    { "name": "WordPress", "version": "6.4.1", "categories": [{"name":"CMS"}] },
    { "name": "PHP", "version": "8.1" },
    { "name": "MySQL" },
    { "name": "Cloudflare" },
    { "name": "jQuery", "version": "3.7.0" }
  ]
}
```

Pivot: WordPress → [wpscan](wpscan.md); Joomla → [joomscan](joomscan.md); Cloudflare → [wafw00f](wafw00f.md).

---

## ⚠️ Gotchas

- Official CLI project is now archived — newer maintained forks: `wappalyzer-cli`, `webappanalyzer`.
- Can miss frameworks served via JS only — hit the page with a real browser / headless.
- For bulk scanning use [nuclei](nuclei.md) `-as` or [httpx](https://github.com/projectdiscovery/httpx) with tech detection.

---

## 🔗 Related

- [whatweb](whatweb.md) · [nuclei](nuclei.md) · [wpscan](wpscan.md) · [joomscan](joomscan.md) · [wafw00f](wafw00f.md)
