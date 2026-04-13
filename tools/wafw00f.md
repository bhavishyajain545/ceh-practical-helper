# wafw00f — Web Application Firewall detector

> **Is there a WAF in the way?** wafw00f sends crafted probes and fingerprints 150+ known WAFs.

**Install check:** `wafw00f --version`

---

## 🎯 Cheat-flow

```bash
wafw00f https://target.com                 # simple
wafw00f -v https://target.com              # verbose (show probes)
wafw00f -a https://target.com              # find ALL WAFs (don't stop on first)
wafw00f -l                                 # list detectable WAFs
wafw00f -i urls.txt                        # batch
wafw00f -o out.json -f json https://...    # output file
wafw00f -p http://127.0.0.1:8080 https://target.com   # proxy via Burp
wafw00f -H headers.txt https://target.com  # custom headers
```

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-v` | Verbose |
| `-a` | Find all WAFs (slower) |
| `-l` | List all WAF signatures |
| `-i <file>` | Input list of URLs |
| `-o <file>` | Output |
| `-f <csv/json/text>` | Output format |
| `-p <proxy>` | HTTP(S) proxy |
| `-H <file>` | Headers file |
| `-t <name>` | Test for a specific WAF |
| `-V` | Version |
| `-r` | Don't follow redirects |

---

## 📋 Typical output

```
[*] Checking https://target.com
[+] The site https://target.com is behind Cloudflare (Cloudflare Inc.) WAF.
[~] Number of requests: 2
```

Detects e.g. **Cloudflare, AWS (ELB), Akamai, Imperva SecureSphere, ModSecurity, Sucuri, Barracuda, Fortinet, F5 BIG-IP ASM, Citrix Netscaler, Wallarm**.

---

## ⚠️ Gotchas

- A "no WAF detected" result doesn't mean *no* WAF — it means no known signature matched. Try `-a` and manual probing.
- Some WAFs only activate on malicious payloads — wafw00f sends them intentionally, so traffic may be logged.
- Behind a CDN? wafw00f will report the CDN (e.g. Cloudflare) which is usually *also* the WAF.
- Combine with [whatweb](whatweb.md) and [nmap](nmap.md) for complete stack ID.

---

## 🔗 Related

- [whatweb](whatweb.md) · [nuclei](nuclei.md) · [burpsuite](burpsuite.md)
