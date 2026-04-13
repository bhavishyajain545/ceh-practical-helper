# 11 Web Servers — copy-paste commands

> Replace `<IP>` / `<URL>` with target. Wordlists assumed at `/usr/share/wordlists/`.

## Banner grab (fastest fingerprint)

```bash
# Raw headers — look for Server: and X-Powered-By:
curl -I http://<IP>

# Full verbose (TLS info too)
curl -Iv https://<IP>

# If only nc is available
echo -e "HEAD / HTTP/1.0\r\n\r\n" | nc <IP> 80
```

→ See [curl.md](../tools/curl.md)

## Tech stack fingerprint

```bash
# Level 3 = aggressive, shows versions
whatweb -a 3 http://<IP>

# Bulk
whatweb -i targets.txt --log-brief out.txt
```

→ See [whatweb.md](../tools/whatweb.md)

## WAF detection

```bash
wafw00f http://<IP>
wafw00f -a http://<IP>        # list ALL matching WAFs
```

## Known-vuln scan (nikto — the money tool)

```bash
# Basic
nikto -h http://<IP>

# HTTPS / custom port
nikto -h https://<IP>:8443

# Save output
nikto -h http://<IP> -o nikto.txt -Format txt

# Through a proxy (burp)
nikto -h http://<IP> -useproxy http://127.0.0.1:8080
```

→ See [nikto.md](../tools/nikto.md)

## Directory / file brute force

```bash
# gobuster — the default
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt -t 50

# gobuster with extensions
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt -x php,html,txt,bak

# ffuf — faster, recursive
ffuf -u http://<IP>/FUZZ -w /usr/share/seclists/Discovery/Web-Content/common.txt -mc 200,301,302,403

# ffuf with extensions
ffuf -u http://<IP>/FUZZ -w common.txt -e .php,.bak,.old,.zip

# dirb — classic fallback
dirb http://<IP> /usr/share/wordlists/dirb/common.txt
```

→ See [gobuster.md](../tools/gobuster.md) · [ffuf.md](../tools/ffuf.md)

## Virtual host discovery

```bash
gobuster vhost -u http://<IP> -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
ffuf -u http://<IP> -H "Host: FUZZ.<DOMAIN>" -w subdomains.txt -fs <size-to-filter>
```

## Manual must-checks

```bash
curl http://<IP>/robots.txt
curl http://<IP>/sitemap.xml
curl http://<IP>/.git/HEAD
curl http://<IP>/server-status
curl http://<IP>/.env
```

## HTTP methods

```bash
# Enumerate allowed methods (look for PUT/DELETE/TRACE)
curl -X OPTIONS http://<IP> -i
nmap -p 80 --script http-methods <IP>
```
