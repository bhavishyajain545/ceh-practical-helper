# 02 Scanning — copy-paste commands

> Replace `<IP>` with the target. Replace `<RANGE>` with e.g. `10.10.10.0/24`.

## Live host discovery

```bash
# Ping sweep a /24
nmap -sn <RANGE> -oN live.txt

# Count alive hosts
nmap -sn <RANGE> | grep -c "Host is up"

# When ICMP is blocked — use ARP (LAN) or TCP-SYN ping
sudo nmap -PR <RANGE>                    # ARP, fastest on LAN
nmap -PS22,80,443 <RANGE>                # TCP SYN ping
```

→ See [nmap.md → host discovery](../tools/nmap.md#host-discovery)

## Port scan

```bash
# Full port scan + versions + safe scripts (the "always run first")
nmap -sV -sC -p- -T4 -oA full <IP>

# Fast top 1000
nmap -sV --top-ports 1000 -oN quick.txt <IP>

# Stealth / firewalled
sudo nmap -sS -Pn -T4 -p- <IP>

# UDP top 20 (slow)
sudo nmap -sU --top-ports 20 -oN udp.txt <IP>
```

→ See [nmap.md → scan techniques](../tools/nmap.md#scan-techniques)

## Service version (the most common exam question)

```bash
nmap -sV -p <PORT> <IP>
# Read the VERSION column. Copy verbatim into your answer.
```

→ See [nmap.md → flag -sV](../tools/nmap.md#scan-techniques)

## OS detection

```bash
sudo nmap -O <IP>
# Look for "OS details:" line
```

## Vuln scan

```bash
nmap --script vuln -p- -oN vuln.txt <IP>
```

## Service-specific power scans

```bash
# SMB — everything you'd want
nmap -p 445 --script "smb-os-discovery,smb-enum-shares,smb-enum-users,smb-vuln-*" <IP>

# HTTP — basics
nmap -p 80,443,8080,8443 --script "http-title,http-headers,http-methods,http-enum" <IP>

# FTP — anon + version
nmap -p 21 --script "ftp-anon,ftp-syst,ftp-vsftpd-backdoor" <IP>

# SSL/TLS
nmap -p 443 --script "ssl-cert,ssl-enum-ciphers,ssl-heartbleed" <IP>

# SNMP
sudo nmap -sU -p 161 --script "snmp-info,snmp-brute,snmp-sysdescr" <IP>

# DNS zone transfer (huge win if it works)
nmap -p 53 --script dns-zone-transfer --script-args dns-zone-transfer.domain=<DOMAIN> <IP>
```

→ Full NSE table: [nmap.md → NSE scripts](../tools/nmap.md#nse-scripts-the-money-section)

## Output — always save

```bash
nmap -oA basename ...   # writes basename.nmap, .xml, .gnmap
```
