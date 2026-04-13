# 04 Vulnerability Analysis — copy-paste commands

> Replace `<IP>` with the target, `<PRODUCT>` with a service name, `<VERSION>` with the banner string.

## Web — nikto

```bash
# Basic scan
nikto -h http://<IP>

# Specific port
nikto -h http://<IP>:8080

# HTTPS, skip cert check
nikto -h https://<IP> -ssl

# Save output
nikto -h http://<IP> -o nikto.txt -Format txt
```

→ See [nikto.md](../tools/nikto.md)

## Network — nmap vuln scripts

```bash
# Broad sweep (slow but thorough)
nmap --script vuln -p- -oN vuln.txt <IP>

# Limit to top 1000 ports (fast)
nmap --script vuln --top-ports 1000 <IP>

# Category-specific
nmap -p 445 --script "smb-vuln-*" <IP>
nmap -p 443 --script "ssl-heartbleed,ssl-poodle,ssl-ccs-injection" <IP>
nmap -p 80,443 --script "http-vuln-*" <IP>
```

→ See [nmap.md NSE](../tools/nmap.md#nse-scripts-the-money-section)

## searchsploit — offline exploit-db

```bash
# Search by product + version
searchsploit <PRODUCT> <VERSION>
searchsploit vsftpd 2.3.4
searchsploit proftpd 1.3.5

# Include CVE links
searchsploit -w vsftpd 2.3.4

# Copy an exploit locally (don't edit the DB copy)
searchsploit -m <PATH-FROM-OUTPUT>
# e.g. searchsploit -m exploits/unix/remote/17491.rb

# Search CVE directly
searchsploit --cve CVE-2017-0144

# Update the DB
searchsploit -u
```

→ See [searchsploit.md](../tools/searchsploit.md)

## Banner → CVE workflow

```bash
# 1. Get the banner
nmap -sV -p <PORT> <IP>

# 2. Feed it to searchsploit (strip the "dash", keep the space)
searchsploit "<PRODUCT> <VERSION>"

# 3. Cross-check NVD
#   https://nvd.nist.gov/vuln/search/results?query=<PRODUCT>+<VERSION>

# 4. If CEH wants the CVE, copy verbatim: CVE-YYYY-NNNNN
```

## OpenVAS / Greenbone (GVM)

```bash
# Start the stack (on Kali)
sudo gvm-start

# Default UI
xdg-open https://127.0.0.1:9392
# creds shown by `sudo gvm-setup` on first run

# From the UI: Scans → Tasks → New Task → Full and Fast → Target = <IP>
```

→ See [openvas.md](../tools/openvas.md)

## grep the exploit-db tree directly

```bash
# Faster than searchsploit for odd strings
grep -ri "eternalblue" /usr/share/exploitdb/exploits/ | head
ls /usr/share/exploitdb/exploits/windows/remote/ | grep -i smb
```

## Known-vuln quick references

```bash
# MS17-010 / EternalBlue
nmap -p 445 --script smb-vuln-ms17-010 <IP>

# Heartbleed
nmap -p 443 --script ssl-heartbleed <IP>

# Shellshock (CGI)
nmap -p 80 --script http-shellshock --script-args uri=/cgi-bin/test.cgi <IP>

# Log4Shell — no reliable nmap NSE; use nuclei or manual
```

## Save everything

```bash
mkdir -p vuln && cd vuln
nikto -h http://<IP> -o nikto.txt
nmap --script vuln -oN nmap-vuln.txt <IP>
searchsploit <PRODUCT> <VERSION> > searchsploit.txt
```
