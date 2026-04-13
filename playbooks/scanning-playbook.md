# Playbook — Scanning

> "I just got a scanning question. What now?"

```
                ┌─────────────────────────┐
                │  Read the question 2x   │
                └──────────┬──────────────┘
                           │
              ┌────────────┴────────────┐
              │ What does it ask for?   │
              └────────────┬────────────┘
                           │
        ┌──────────────────┼─────────────────────────────┐
        │                  │                             │
   ┌────▼────┐        ┌────▼─────┐                 ┌─────▼─────┐
   │ Live    │        │ Open     │                 │ Service / │
   │ hosts?  │        │ ports?   │                 │ version / │
   │         │        │          │                 │ vuln?     │
   └────┬────┘        └────┬─────┘                 └─────┬─────┘
        │                  │                             │
        ▼                  ▼                             ▼
  nmap -sn         nmap -p- -T4               nmap -sV -sC -p-
  <RANGE>          <IP>                        <IP>
        │                  │                             │
        ▼                  ▼                             ▼
  count "Host       count "open"                Read VERSION
  is up" lines      lines                       column / NSE
                                                output
```

## Service-specific shortcuts

| You see... | Jump to |
|---|---|
| Port 21 → FTP | `nmap -p 21 --script "ftp-anon,ftp-vsftpd-backdoor" -sV <IP>` |
| Port 22 → SSH | `nmap -p 22 -sV <IP>` then check version for known CVEs |
| Port 25 → SMTP | `nmap -p 25 --script "smtp-enum-users,smtp-commands" <IP>` |
| Port 53 → DNS | `dig axfr <DOMAIN> @<IP>` |
| Port 80/443 → HTTP | `nmap -p 80,443 --script "http-title,http-headers,http-enum" <IP>` |
| Port 110 → POP3 | `nmap -p 110 -sV <IP>` |
| Port 139/445 → SMB | `nmap -p 445 --script "smb-os-discovery,smb-enum-shares,smb-vuln-*" <IP>` |
| Port 161 → SNMP (UDP) | `sudo nmap -sU -p 161 --script "snmp-info,snmp-brute" <IP>` |
| Port 3306 → MySQL | `nmap -p 3306 --script "mysql-info,mysql-empty-password" <IP>` |
| Port 3389 → RDP | `nmap -p 3389 --script "rdp-enum-encryption,rdp-vuln-*" <IP>` |

## When the easy path fails

1. Add `-Pn` (skip ping)
2. Add `-T4` if too slow, `-T2` if being throttled
3. Try `-sT` if you're not root
4. Try `-f` (fragment) or `--source-port 53` for firewall evasion
5. UDP services? `sudo nmap -sU -p <port>`
6. Need stealth? `-sS -T2 -f`

→ See full nmap reference: [tools/nmap.md](../tools/nmap.md)
