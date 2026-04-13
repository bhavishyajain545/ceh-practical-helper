# First 5 commands you run on EVERY target

> The moment you see an IP in a question, run these in background while you read the question carefully. By the time you've understood the question, the scans are half-done.

```bash
# 1. Full TCP scan with versions + safe scripts (5–10 min, run in background)
nmap -sV -sC -p- -T4 -oA full <IP> &

# 2. UDP top 20 (slow but catches SNMP/DNS/TFTP)
sudo nmap -sU --top-ports 20 -oN udp.txt <IP> &

# 3. Quick top-1000 to give you immediate answers while #1 runs
nmap -sV --top-ports 1000 -oN quick.txt <IP>

# 4. If web ports show up, immediately:
nikto -h http://<IP> -o nikto.txt &
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt -o gobuster.txt &

# 5. If SMB (445) shows up:
nmap -p 445 --script "smb-os-discovery,smb-enum-shares,smb-enum-users,smb-vuln-*" <IP>
enum4linux -a <IP>
```

## After the scans return

| Found... | Next step |
|---|---|
| FTP (21) | `nmap -p 21 --script ftp-anon,ftp-vsftpd-backdoor -sV <IP>` |
| SSH (22) | note version → check exploit-db |
| SMTP (25) | `nmap -p 25 --script smtp-enum-users <IP>` |
| HTTP/S (80/443/8080) | nikto + gobuster (already running) |
| SMB (445) | `enum4linux -a <IP>` |
| SNMP (161) | `snmpwalk -c public -v 1 <IP>` |
| MySQL (3306) | try `mysql -h <IP> -u root -p` (blank password) |
| RDP (3389) | check encryption + try hydra |

## Key trick

**Always use `-oA <name>`** so you can `grep` outputs later. Working memory in a 6-hour exam is your enemy — make everything searchable.
