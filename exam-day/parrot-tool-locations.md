# Parrot OS — where stuff lives

> iLabs Cyber Range gives you Parrot Security with all common pentest tools pre-installed.

## Wordlists

| Wordlist | Path |
|---|---|
| rockyou (THE password list) | `/usr/share/wordlists/rockyou.txt` (may be `.gz` — `gunzip` it) |
| dirb common | `/usr/share/wordlists/dirb/common.txt` |
| dirbuster medium | `/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt` |
| seclists | `/usr/share/seclists/` |
| nmap NSE scripts | `/usr/share/nmap/scripts/` |

## Tools (verify install with `which <tool>`)

- nmap, masscan, hping3, netdiscover
- nikto, dirb, gobuster, ffuf, wfuzz
- hydra, medusa, john, hashcat, crunch
- sqlmap, wpscan, joomscan
- enum4linux, smbclient, snmpwalk, ldapsearch
- aircrack-ng suite (airmon-ng, airodump-ng, aireplay-ng)
- wireshark, tcpdump, ettercap, bettercap
- metasploit (`msfconsole`, `msfvenom`)
- steghide, exiftool, binwalk, foremost
- openssl, hashid, hash-identifier
- responder, impacket-* scripts (`impacket-secretsdump`, etc.)

## Useful one-liners

```bash
# Find a tool
which <tool>            # is it on PATH?
locate <name>           # search filesystem
apropos <topic>         # search man pages

# Update locate db once
sudo updatedb

# Decompress rockyou if needed
gunzip /usr/share/wordlists/rockyou.txt.gz

# Find an NSE script
ls /usr/share/nmap/scripts/ | grep <keyword>

# Find a Metasploit module
msfconsole -q -x "search <keyword>; exit"
```

## Browser

Firefox is preinstalled. Open this repo via `file:///` URL or the GitHub URL — both work in iLabs.
