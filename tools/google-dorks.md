# google-dorks — search engine operators cheat-sheet

> **Passive recon via Google (and Bing / DuckDuckGo).** CEH Practical loves asking you to construct the "right dork".

---

## 🎯 Core operators

| Operator | Meaning | Example |
|---|---|---|
| `site:` | Limit to domain | `site:target.com` |
| `-site:` | Exclude domain | `site:target.com -site:blog.target.com` |
| `inurl:` | Term in URL | `inurl:admin` |
| `allinurl:` | All terms in URL | `allinurl: admin login` |
| `intitle:` | Term in `<title>` | `intitle:"index of"` |
| `allintitle:` | All terms in title | `allintitle: admin portal` |
| `intext:` | Term in body | `intext:"sql syntax"` |
| `filetype:` / `ext:` | File extension | `filetype:pdf` |
| `cache:` | Google's cached copy | `cache:target.com` |
| `link:` | Pages linking here (deprecated) | |
| `related:` | Similar sites | `related:target.com` |
| `info:` | Page info | |
| `"..."` | Exact phrase | `"internal use only"` |
| `*` | Wildcard | `"password *"` |
| `OR` `\|` | Either | `admin OR login` |
| `AND` | Both (implicit) | |
| `..` | Numeric range | `camera 2020..2024` |
| `define:` | Definition | |

Combine freely: `site:target.com filetype:pdf intext:"confidential"`.

---

## 🎯 Classic dorks (memorise)

**Finding sensitive files:**
```
site:target.com filetype:env
site:target.com filetype:log
site:target.com filetype:sql
site:target.com ext:bak | ext:old | ext:backup
site:target.com inurl:wp-config
site:target.com filetype:pdf intext:"confidential"
site:target.com ext:xls OR ext:xlsx password
```

**Exposed directories:**
```
site:target.com intitle:"index of"
site:target.com intitle:"index of" "parent directory"
intitle:"index of" "passwd"
intitle:"index of" /.git
```

**Login portals:**
```
site:target.com inurl:admin
site:target.com inurl:login
site:target.com intitle:"admin login"
intitle:"phpMyAdmin" inurl:index.php
```

**Cameras / IoT:**
```
intitle:"webcamXP 5"
inurl:"view/index.shtml"
intitle:"Live View / - AXIS"
```

**Error messages (SQLi/PHP):**
```
intext:"sql syntax near" intext:"on line"
intext:"Warning: include(" "on line"
```

**Source code / secrets:**
```
site:github.com "target.com" password
site:pastebin.com "target.com"
site:trello.com password
```

**Cloud buckets:**
```
site:s3.amazonaws.com "target"
site:blob.core.windows.net "target"
site:storage.googleapis.com "target"
```

**VPN / internal:**
```
inurl:"/+CSCOE+/logon.html"              (Cisco AnyConnect)
intitle:"Outlook Web App" inurl:/owa/
intitle:"RD Web Access"
```

---

## 🗂 Google Hacking Database (GHDB)

Thousands of curated dorks at https://www.exploit-db.com/google-hacking-database — searchable by category. Bookmark for CEH Practical.

---

## ⚠️ Gotchas

- Google aggressively rate-limits + serves CAPTCHAs on repeated dork queries. Use DuckDuckGo (`!g` bang) or Bing when blocked.
- `link:` and `info:` are effectively dead.
- `+` is gone (Google+ namespace).
- Results are cached — use `cache:` when the live page is down.
- **Legal:** passively indexed content is fair game; actively interacting with it (downloading a DB) may not be.

---

## 🔗 Related

- [shodan](shodan.md) · [theharvester](theharvester.md) · [recon-ng](recon-ng.md)
