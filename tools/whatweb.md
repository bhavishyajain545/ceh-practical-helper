# whatweb — web stack fingerprinter

> **One-shot CMS / framework / version ID.** Answers "what is the target running?" in 2 seconds.

**Install check:** `whatweb --version`

---

## 🎯 Cheat-flow

| Task | Command |
|---|---|
| Quick scan | `whatweb http://target/` |
| Aggressive (more plugins, more requests) | `whatweb -a 3 http://target/` |
| Heavy (all plugins, noisy) | `whatweb -a 4 http://target/` |
| Multiple targets | `whatweb -i targets.txt` |
| Verbose (show evidence) | `whatweb -v http://target/` |
| Output JSON | `whatweb --log-json=out.json http://target/` |
| Output XML | `whatweb --log-xml=out.xml http://target/` |
| Follow redirects always | `whatweb --follow-redirect=always http://target/` |
| Aggression per-host | `whatweb -a 3 http://target/,http://target2/` |
| Use proxy (Burp) | `whatweb --proxy 127.0.0.1:8080 http://target/` |
| List plugins | `whatweb -l` |
| Run one plugin | `whatweb -p WordPress http://target/` |

---

## 🔑 Aggression levels

| Level | Behavior |
|---|---|
| `-a 1` | Passive — one GET, minimal plugins (default) |
| `-a 2` | Not implemented |
| `-a 3` | Aggressive — extra requests to confirm guesses |
| `-a 4` | Heavy — all plugins, many requests |

Higher = more accurate but noisier.

---

## 📋 Recipes

```bash
# 1. Normal recon — identify CMS and versions
whatweb -a 3 -v http://target/

# 2. Sweep a subnet from nmap output
cut -d' ' -f2 live.txt | xargs -I{} whatweb -a 1 http://{}

# 3. JSON for later grepping
whatweb --log-json=wh.json -a 3 http://target/
jq '.[] | .plugins | keys' wh.json

# 4. Through Burp for tidy history
whatweb --proxy 127.0.0.1:8080 -a 3 http://target/
```

**Typical output:**
```
http://target/ [200 OK] Apache[2.4.41], Country[UNITED STATES][US],
  HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.10.10.5],
  JQuery[3.5.1], Script, Title[Welcome], WordPress[5.7.2]
```

Read the plugin names like a menu: WordPress 5.7.2 → pivot to [wpscan](wpscan.md).

---

## ⚠️ Gotchas

- Default `-a 1` is quiet but under-detects — use `-a 3` for exam questions.
- Will follow redirects only with `--follow-redirect=always`.
- 403/401 pages still produce useful headers — don't discard them.
- Needs the `http://` prefix.

---

## 🔗 Related

- [wafw00f](wafw00f.md) · [wpscan](wpscan.md) · [nikto](nikto.md) · [nuclei](nuclei.md) · [wappalyzer](wappalyzer.md)
