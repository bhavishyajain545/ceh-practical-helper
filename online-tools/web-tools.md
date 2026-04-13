# 🌍 Web Tools (Online)

> URL/header/JWT decode, vuln databases, header inspection — for webapp questions.

## JWT (JSON Web Token)

| Tool | URL | Use |
|---|---|---|
| **JWT.io** | https://jwt.io/ | **The one.** Decode header/payload/signature. Verify with secret. Switch alg. |
| **JWT-Cracker (web)** | https://jwt-cracker.online/ | Brute force HS256 secret |
| **token.dev** | https://token.dev/ | Alternate JWT decoder |

**JWT attacks via JWT.io:**
1. Paste token → see decoded payload
2. Try changing `alg` to `none` → re-encode
3. Try weak secret like `secret`, `key`, `password`
4. Modify payload (e.g. `"admin": false` → `true`) → re-sign with known secret

---

## URL / parameter encoding

| Tool | URL | Use |
|---|---|---|
| **URL Decoder** | https://www.urldecoder.org/ | Decode/encode URL params |
| **CyberChef** | https://gchq.github.io/CyberChef/ | "URL Decode" / "URL Encode" recipe |

---

## HTTP header inspection

| Tool | URL | Use |
|---|---|---|
| **SecurityHeaders.com** | https://securityheaders.com/ | Header grade + missing security headers |
| **Webhint** | https://webhint.io/scanner/ | Best practices scan |
| **Hardenize** | https://www.hardenize.com/ | TLS + DNS + email headers |
| **HTTP Status Checker** | https://httpstatus.io/ | Bulk URL status check |
| **REQBin** | https://reqbin.com/ | Send custom HTTP requests from browser |

---

## Vulnerability databases

| Tool | URL | Use |
|---|---|---|
| **Exploit-DB** | https://www.exploit-db.com/ | Search by CVE, CVE-XXXX-XXXX, or software name |
| **CVE Details** | https://www.cvedetails.com/ | Full CVE with CVSS, references, exploits |
| **NVD** | https://nvd.nist.gov/ | NIST official CVE database |
| **Vulners** | https://vulners.com/ | Aggregated DB with CPE search |
| **Rapid7 DB** | https://www.rapid7.com/db/ | Metasploit module search |
| **CVE Mitre** | https://cve.mitre.org/ | Original source |
| **Snyk DB** | https://security.snyk.io/ | Package-level vulns (npm/pypi/etc) |

**Workflow for "find CVE for service version" CEH question:**
1. Got service version from nmap (e.g. `vsftpd 2.3.4`)
2. Google: `vsftpd 2.3.4 exploit-db`
3. OR direct: `https://www.exploit-db.com/search?q=vsftpd+2.3.4`
4. Get CVE-XXXX-XXXX
5. Search Rapid7 DB for Metasploit module name

---

## CMS detection

| Tool | URL | Use |
|---|---|---|
| **WhatCMS** | https://whatcms.org/ | CMS identification by URL |
| **BuiltWith** | https://builtwith.com/ | Full tech stack detection |
| **Wappalyzer (web)** | https://www.wappalyzer.com/lookup/ | Tech detection (also browser ext) |
| **W3Techs** | https://w3techs.com/sites | Tech of major sites |

---

## SSL/TLS test

| Tool | URL | Use |
|---|---|---|
| **SSL Labs** | https://www.ssllabs.com/ssltest/ | **Gold standard.** Full SSL grade + cert details + cipher list |
| **TestSSL.sh** | https://testssl.sh/ | (terminal — but has online demo) |
| **CryptCheck** | https://cryptcheck.fr/ | Quick SSL grade |
| **HTBridge SSL** | https://www.immuniweb.com/ssl/ | Free PCI/HIPAA compliance check |

---

## XSS / Payload references

| Tool | URL | Use |
|---|---|---|
| **PayloadsAllTheThings** | https://github.com/swisskyrepo/PayloadsAllTheThings | **The bible.** Every payload type categorized |
| **HackTricks** | https://book.hacktricks.xyz/ | Pentest cookbook with payloads |
| **OWASP XSS Cheat Sheet** | https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html | XSS context-by-context |
| **XSS Hunter** | https://xsshunter.trufflesecurity.com/ | Blind XSS payloads with callback |
| **PortSwigger XSS Cheat Sheet** | https://portswigger.net/web-security/cross-site-scripting/cheat-sheet | Burp's XSS encyclopedia |

---

## SQL injection references

| Tool | URL | Use |
|---|---|---|
| **PayloadsAllTheThings SQLi** | https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection | All injection payloads by DBMS |
| **PortSwigger SQLi Cheat Sheet** | https://portswigger.net/web-security/sql-injection/cheat-sheet | DB-specific syntax |
| **HackTricks SQLi** | https://book.hacktricks.xyz/pentesting-web/sql-injection | Comprehensive walkthrough |

---

## File inclusion / LFI

| Tool | URL | Use |
|---|---|---|
| **LFI Cheat Sheet (HighOn.Coffee)** | https://highon.coffee/blog/lfi-cheat-sheet/ | All LFI payload variations |
| **PayloadsAllTheThings LFI** | https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/File%20Inclusion | Full collection |

---

## Mock APIs / Test endpoints

| Tool | URL | Use |
|---|---|---|
| **httpbin.org** | https://httpbin.org/ | Echo your HTTP requests for testing |
| **webhook.site** | https://webhook.site/ | **Critical for SSRF/blind XSS/callbacks.** Get a unique URL, see hits in real-time |
| **RequestBin** | https://requestbin.com/ | Same idea, alternative |
| **interactsh** | https://app.interactsh.com/ | OOB callback service for blind injection |

**webhook.site usage:** Open URL → get unique URL → use as XSS callback / SSRF target / blind SQLi exfil → see incoming requests live.

---

## ⚠️ Gotchas

- **JWT.io** does signature verification client-side — your secret never leaves the browser
- **CVE numbers vs MS-IDs** — sometimes CEH wants `MS17-010`, sometimes `CVE-2017-0144`. Note both.
- **PayloadsAllTheThings is huge** — bookmark specific subsections, don't try to find on the fly
