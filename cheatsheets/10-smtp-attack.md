# SMTP PORT OPEN (25) — Ab Kya Karu?

---

## Pehle Samjho: SMTP Kya Hai?

SMTP = **Simple Mail Transfer Protocol**. Ye email BHEJNE ke liye use hota hai.
Jab tum Gmail se kisi ko email bhejte ho, toh peeche SMTP kaam karta hai.

**Key Facts:**
- Port: **25 (TCP)** — default SMTP port
- Port 587 = SMTP with STARTTLS (encrypted submission)
- Port 465 = SMTPS (SSL encrypted)
- SMTP sirf email BHEJNE ke liye hai (receive karne ke liye IMAP/POP3 hota hai)
- CEH exam mein SMTP = **User Enumeration** (90% yahi poochte hain)
- SMTP commands plaintext mein jaate hain — PCAP mein dikh jaate hain

### SMTP Kaise Kaam Karta Hai (Simple Version):
```
Tumhara Computer --SMTP--> Tumhara Mail Server --SMTP--> Receiver ka Mail Server --IMAP/POP3--> Receiver
```

### Important SMTP Commands (Ye Jaano):
| Command    | Kya karta hai                                      |
|------------|---------------------------------------------------|
| HELO       | Server se hello bolo (connection start)            |
| EHLO       | Extended hello (features bhi poocho)               |
| VRFY       | **Verify** — check karo user exist karta hai ya nahi |
| EXPN       | **Expand** — mailing list ke members dikhao        |
| RCPT TO    | **Recipient** — email kisko bhejna hai              |
| MAIL FROM  | Email kahan se aa raha hai                          |
| DATA       | Email body likhna start karo                        |
| QUIT       | Connection band karo                                |

### User Enumeration Kaise Kaam Karta Hai?

SMTP server pe VRFY command se pata lagta hai ki koi user exist karta hai ya nahi:
- `VRFY admin` → `250 admin@target.com` (USER EXISTS!)
- `VRFY randomxyz` → `550 User not found` (user nahi hai)

**Ye kyun important hai?** Valid usernames milne ke baad SSH/FTP/SMB pe in usernames se brute force kar sakte ho!

---

## STEP 1: Confirm SMTP Open Hai

```bash
nmap -sV -p 25 <IP>
# Extended ports bhi check karo
nmap -sV -p 25,587,465 <IP>
```

**Output mein kya dekho:**
- `25/tcp open smtp` + version info (Postfix, Sendmail, Microsoft ESMTP)
- Banner mein domain name bhi dikh sakta hai (useful for later)

---

## STEP 2: User Enumeration — Automated Tool (Sabse Common Task)

```bash
# VRFY method se (sabse common)
smtp-user-enum -M VRFY -U /usr/share/wordlists/metasploit/unix_users.txt -t <IP>

# EXPN method se (mailing lists expand karo)
smtp-user-enum -M EXPN -U /usr/share/wordlists/metasploit/unix_users.txt -t <IP>

# RCPT method se (agar VRFY disabled hai)
smtp-user-enum -M RCPT -U /usr/share/wordlists/metasploit/unix_users.txt -t <IP>

# Nmap script se
nmap --script smtp-enum-users -p 25 <IP>

# Custom wordlist banana ho toh
smtp-user-enum -M VRFY -U /usr/share/wordlists/seclists/Usernames/top-usernames-shortlist.txt -t <IP>
```

**Flags explained:**
- `-M VRFY` = method (VRFY, EXPN, ya RCPT)
- `-U users.txt` = username wordlist
- `-t <IP>` = target IP

**Output mein kya dekho:**
```
root          exists
admin         exists
msfadmin      exists
user          exists
```
Ye usernames note karo! SSH/FTP pe brute force mein kaam aayenge.

**IMPORTANT:** Agar VRFY se kuch nahi aaya → RCPT method try karo. Kuch servers VRFY disable karte hain lekin RCPT se pata chal jaata hai.

### Teeno Methods ka Fark:

| Method | Kaise kaam karta hai | Kab use karo |
|--------|---------------------|-------------|
| VRFY   | Directly poochta hai "ye user hai?" | Pehle ye try karo |
| EXPN   | Mailing list expand karta hai | VRFY block ho toh |
| RCPT   | Fake email bhejne ka try karta hai | Dono fail ho toh |

---

## STEP 3: Manual SMTP Interaction (Haath Se Try Karo)

Kabhi kabhi manually interact karna padta hai — especially exam mein dikhane ke liye:

```bash
nc <IP> 25
```

Ab ye commands ek ek karke type karo:

```
HELO test.com                    # Server se hello bolo
VRFY admin                       # Check: admin user hai?
VRFY root                        # Check: root user hai?
VRFY msfadmin                    # Check: msfadmin user hai?
VRFY randomfakeuser123           # Check: ye user hai? (control test)
```

**Output decode karo:**
- `250` = User EXISTS! (Naam note karo)
- `252` = Cannot verify but will accept (shayad hai)
- `550` = User does NOT exist
- `502` = Command not implemented (VRFY disabled hai, RCPT try karo)
- `503` = Bad sequence (pehle HELO bhejo)

### Manual Email Bhejne Ka Try (Open Relay Test):
```
HELO test.com
MAIL FROM:<hacker@evil.com>
RCPT TO:<admin@target.com>
DATA
Subject: Test Email
This is a test.
.
QUIT
```

**WHY manual interaction:** Exam mein screenshot dikhane ke liye useful hai. Aur kuch tools jab fail hote hain toh manually karna padta hai.

---

## STEP 4: Open Relay Check

**Open Relay kya hai?** Ek SMTP server jo KISI BHI source se KISI KO BHI email bhejne deta hai bina authentication ke. Spammers ka sapna!

```bash
# Nmap script se check karo
nmap --script smtp-open-relay -p 25 <IP>

# Manual check: nc se connect karke bahar ka email bhejo
nc <IP> 25
HELO test.com
MAIL FROM:<fake@gmail.com>
RCPT TO:<someone@gmail.com>    # Bahar ka address
# Agar 250 OK aaye → OPEN RELAY hai!
```

**Output mein kya dekho:**
- nmap output: `Server is an open relay` → Haan, open relay hai
- Manual: `250 OK` on RCPT TO external address → open relay confirmed
- `550 Relaying denied` → open relay NAHI hai (good configuration)

**Exam mein:** Open relay rarely main question hota hai, but enumeration ke saath mention kar do.

---

## STEP 5: Nmap SMTP Scripts (Quick Scan)

```bash
# Saare useful SMTP scripts ek saath
nmap --script smtp-enum-users,smtp-open-relay,smtp-commands,smtp-vuln-cve2010-4344 -p 25 <IP>

# SMTP commands check karo (kya kya supported hai)
nmap --script smtp-commands -p 25 <IP>
```

**`smtp-commands` output mein kya dekho:**
```
VRFY, EXPN, HELP, ETRN, ...
```
Agar VRFY listed hai → user enumeration possible hai.
Agar EXPN listed hai → mailing list expand possible hai.

---

## STEP 6: PCAP Mein Email Sniffing

Agar question mein PCAP file diya hai aur pooch rahe hain email content ya creds:

### Wireshark (GUI):
```
1. PCAP kholo Wireshark mein
2. Filter lagao: smtp
3. Kisi SMTP packet pe right-click
4. "Follow" → "TCP Stream"
5. Poori email conversation dikhegi — from, to, subject, body sab!
```

### tshark (Command Line):
```bash
# SMTP traffic filter karo
tshark -r capture.pcap -Y "smtp" -T fields -e smtp.req.command -e smtp.req.parameter

# TCP stream follow karo
tshark -r capture.pcap -z follow,tcp,ascii,0 -q

# Email addresses dhundo
tshark -r capture.pcap -Y "smtp.req.command == MAIL || smtp.req.command == RCPT" -T fields -e smtp.req.parameter
```

**PCAP mein kya dhundo:**
- `MAIL FROM:` — sender ka email address
- `RCPT TO:` — receiver ka email address
- `Subject:` — email ka subject
- `DATA` ke baad — email body ka content
- Authentication strings (Base64 encoded ho sakte hain — decode karo!)

### Base64 Credentials Decode:
```bash
# PCAP mein AUTH LOGIN ke baad Base64 strings dikhen toh:
echo "YWRtaW4=" | base64 -d    # admin
echo "cGFzc3dvcmQ=" | base64 -d   # password
```

---

## Common Mistakes (Mat Karna Ye)

1. **Sirf VRFY try kiya, RCPT nahi** — Bahut saare servers VRFY disable karte hain. Agar VRFY se `502` aaye toh RCPT method try karo: `smtp-user-enum -M RCPT`

2. **nc se connect karke seedha VRFY bhej diya** — Pehle `HELO test.com` bhejna ZAROORI hai! Bina HELO ke server commands accept nahi karega.

3. **Mile hue usernames ka kuch nahi kiya** — SMTP se users mile? Toh SSH/FTP/Telnet pe in usernames se brute force karo! SMTP sirf enumeration ke liye hai.

4. **PCAP mein Base64 strings ignore kar diye** — SMTP AUTH mein credentials Base64 mein hote hain. `base64 -d` se decode karo.

5. **Port 25 pe direct email bhejne lage** — Pehle enumeration karo (VRFY). Open relay check secondary task hai.

6. **Galat wordlist use ki** — `/usr/share/wordlists/metasploit/unix_users.txt` best hai SMTP enum ke liye. rockyou.txt PASSWORDS ki list hai, usernames ki nahi!

---

## Quick Decision Tree

```
Port 25 Open (SMTP) hai?
|
+-- PCAP file diya hai?
|   |
|   +-- YES --> Wireshark: filter "smtp"
|   |           Follow TCP Stream
|   |           Email content + creds dhundo
|   |           Base64 strings decode karo
|   |
|   +-- NO --> Aage badho
|
+-- USER ENUMERATION KARO (main task!)
|   |
|   +-- smtp-user-enum -M VRFY -U unix_users.txt -t <IP>
|   |   |
|   |   +-- Users mile? --> NOTE KARO! SSH/FTP pe brute force
|   |   |
|   |   +-- 502 error? --> VRFY disabled hai
|   |       |
|   |       +-- RCPT method try karo
|   |           smtp-user-enum -M RCPT -U users.txt -t <IP>
|   |
|   +-- Manual check bhi karo:
|       nc <IP> 25 --> HELO test.com --> VRFY admin
|
+-- Open Relay Check (secondary)
|   nmap --script smtp-open-relay -p 25 <IP>
|
+-- Mile hue usernames se NEXT STEP:
    SSH brute force: hydra -L smtp_users.txt -P rockyou.txt <IP> ssh
    FTP brute force: hydra -L smtp_users.txt -P rockyou.txt <IP> ftp
```
