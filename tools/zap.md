# zap — OWASP ZAP

> **The open-source Burp.** Proxy, active + passive scanner, spider, fuzzer, API.

**Launch:** `zaproxy &` or `owasp-zap &`  |  headless: `zap.sh -daemon -port 8090`

---

## 🎯 Cheat-flow (GUI)

1. Launch ZAP → "Manual Explore" or "Automated Scan".
2. Set the target URL → ZAP spiders it.
3. Run **Active Scan** on the target in the Sites tree.
4. Check the **Alerts** tab for findings.

Browser proxy: `127.0.0.1:8080` (or 8090 if headless). Install ZAP CA from **Tools → Options → Dynamic SSL certs → Save** → import into browser.

---

## 🔑 Headless / CLI (great for CI)

```bash
# Baseline passive scan
zap-baseline.py -t https://target.com -r report.html

# Full active scan
zap-full-scan.py -t https://target.com -r full.html

# API scan (OpenAPI / SOAP / GraphQL)
zap-api-scan.py -t https://target.com/openapi.json -f openapi -r api.html

# Daemon mode
zap.sh -daemon -port 8090 -config api.key=secret
```

REST API:
```bash
curl "http://127.0.0.1:8090/JSON/ascan/action/scan/?url=https://target.com&apikey=secret"
curl "http://127.0.0.1:8090/JSON/core/view/alerts/?baseurl=https://target.com&apikey=secret"
```

---

## 🧰 Built-in tools (right-click on request in History)

| Tool | Purpose |
|---|---|
| **Manual Request Editor** | ZAP's "Repeater" |
| **Fuzzer** | ZAP's "Intruder" |
| **Forced Browse** | Dir brute (needs OWASP DirBuster lists add-on) |
| **Active Scan** | Automated injection / XSS / LFI / SQLi |
| **Passive Scan** | Continuous, read-only analysis |
| **Spider** | Traditional link crawler |
| **Ajax Spider** | JS-heavy SPA crawler |
| **Scripts** | Auth / payload scripts in JS/Python/Groovy |
| **Add-ons** | Marketplace: HUD, FuzzDB, retire.js, GraphQL |

---

## 📋 Recipes

```bash
# 1. Unauthenticated baseline on a new URL
zap-baseline.py -t https://target.com -r baseline.html

# 2. Full scan of an authenticated area (needs context + auth config via GUI first)
zap-full-scan.py -t https://target.com \
     -z "-config replacer.full_list(0).description=auth \
         -config replacer.full_list(0).enabled=true \
         -config replacer.full_list(0).matchtype=REQ_HEADER \
         -config replacer.full_list(0).matchstr=Authorization \
         -config replacer.full_list(0).replacement=Bearer\\ TOKEN"

# 3. Pipe into Burp-style manual workflow: use Manual Request Editor on any history item
```

---

## ⚠️ Gotchas

- Default port is **8080** GUI vs **8090** headless — don't collide with Burp.
- Active Scan is **noisy and destructive** — never on prod without authorisation.
- Install the CA cert in browser for HTTPS (otherwise you get endless warnings).
- ZAP Docker image `softwaresecurityproject/zap-stable` is the easiest way to run headless.

---

## 🔗 Related

- [burpsuite](burpsuite.md) · [nuclei](nuclei.md) · [nikto](nikto.md) · [sqlmap](sqlmap.md)
