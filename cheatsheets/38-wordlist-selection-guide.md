# KAUNSI WORDLIST USE KARU? — Complete Guide

---

## Ye Kya Hai?
Password cracking, directory bruteforce, username enumeration — sab mein wordlist chahiye.
Galat wordlist = time waste, sahi wordlist = instant crack. Ye guide batayegi kab kaunsi
wordlist use karo, kaise custom wordlist banao, aur rules kaise apply karo.

**Key Concept:** Wordlist = Dictionary of possible passwords/paths/usernames
Tool (john/hashcat/hydra/gobuster) har word try karta hai ek ek karke.

---

## QUICK SELECTION TABLE — Kya Karna Hai, Kaunsi Wordlist Use Karo

| Situation | Wordlist | Path | Lines (approx) |
|---|---|---|---|
| **Password cracking (DEFAULT)** | rockyou.txt | `/usr/share/wordlists/rockyou.txt` | ~14 million |
| **Directory bruteforce (quick)** | common.txt | `/usr/share/wordlists/dirb/common.txt` | ~4,600 |
| **Directory bruteforce (thorough)** | directory-list-2.3-medium | `/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt` | ~220,000 |
| **Directory bruteforce (big)** | directory-list-2.3-big | `/usr/share/wordlists/dirbuster/directory-list-2.3-big.txt` | ~1.2 million |
| **Username enumeration** | unix_users | `/usr/share/wordlists/metasploit/unix_users.txt` | ~160 |
| **SNMP community strings** | snmp-communities | `/usr/share/wordlists/seclists/Discovery/SNMP/common-snmp-community-strings.txt` | ~120 |
| **DNS subdomains** | subdomains-top1million | `/usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt` | ~5,000 |
| **AD usernames** | xato-usernames | `/usr/share/wordlists/seclists/Usernames/xato-net-10-million-usernames.txt` | ~8 million |
| **Web passwords (short)** | 10k-most-common | `/usr/share/wordlists/seclists/Passwords/Common-Credentials/10k-most-common.txt` | ~10,000 |
| **Default credentials** | default-creds | `/usr/share/wordlists/seclists/Passwords/Default-Credentials/` | varies |
| **WiFi passwords** | rockyou.txt | Same as above | ~14 million |

---

## ROCKYOU.TXT — Sabse Important Wordlist

### Kya Hai?
2009 mein RockYou company hack hui thi — 32 million real passwords leak hue. Ye real
users ke real passwords hain, isliye password cracking mein sabse effective hai.

### Compressed Hai? Decompress Karo:
```bash
# Check karo file hai ya nahi:
ls -la /usr/share/wordlists/rockyou.txt
# Agar .gz hai (compressed):
ls -la /usr/share/wordlists/rockyou.txt.gz

# Decompress:
sudo gunzip /usr/share/wordlists/rockyou.txt.gz

# Verify:
wc -l /usr/share/wordlists/rockyou.txt
# Expected: ~14,344,391 lines
```

### Kab Use Karo:
- **Password hash cracking** (MD5, NTLM, SHA, bcrypt, etc.)
- **SSH/FTP/RDP brute force**
- **WiFi handshake cracking**
- **Web login brute force**
- Basically jab bhi password guess karna ho → rockyou FIRST!

### Kab NAHI Kaam Karega:
- Target ka password bohot unique hai (company-specific)
- Password policy strict hai (min 12 chars, special chars required)
- Non-English passwords
- Tab custom wordlist banao (neeche dekho)

---

## DIRECTORY BRUTEFORCE WORDLISTS — Web Paths Ke Liye

### Strategy:
```bash
# Step 1: Quick scan (1-2 min):
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt

# Step 2: Thorough scan (5-10 min):
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

# Step 3: Big scan (agar kuch nahi mila — 30+ min):
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirbuster/directory-list-2.3-big.txt

# Extensions bhi add karo:
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt -x php,html,txt,bak,old,conf
```

### Kaunsi Wordlist Kab:
| Situation | Wordlist | Kyu |
|---|---|---|
| Pehli baar scan | common.txt | Fast, common paths cover karta hai |
| common.txt se kuch nahi mila | directory-list-2.3-medium | 50x zyada paths |
| Medium se bhi nahi mila | directory-list-2.3-big | Nuclear option |
| Specific tech (e.g., Tomcat) | SecLists technology-specific | Targeted paths |

---

## CeWL — WEBSITE SE CUSTOM WORDLIST BANAO

### CeWL Kya Hai?
CeWL (Custom Word List generator) ek spider tool hai — ye target website crawl karta hai
aur saare words extract karta hai. Company names, product names, employee names — ye sab
passwords mein commonly use hote hain!

### Basic Usage:
```bash
cewl http://<TARGET_IP> -m 6 -w custom_wordlist.txt
# -m 6 = Minimum 6 character ke words (chhote words skip)
# -w = Output file
```

### Advanced Options:
```bash
# Depth badhaao (more pages crawl):
cewl http://<TARGET_IP> -m 6 -d 3 -w custom.txt
# -d 3 = 3 levels deep crawl

# Email addresses bhi extract:
cewl http://<TARGET_IP> -m 6 -e -w custom.txt
# -e = Extract email addresses

# With authentication:
cewl http://<TARGET_IP> -m 6 --auth_user admin --auth_pass password -w custom.txt

# Lowercase convert:
cewl http://<TARGET_IP> -m 6 --lowercase -w custom.txt
```

### Kab Use Karo:
- rockyou.txt se crack nahi hua
- Target ki website pe company-specific words hain
- Password policy mein company name ya product name required hai
- CTF mein hint website pe hai

### Example Scenario:
Website pe "MegaCorp", "SecureProject", "AdminPortal" words hain:
```
CeWL output: MegaCorp, SecureProject, AdminPortal, etc.
Possible passwords: MegaCorp123, SecureProject!, AdminPortal2024
```
Rules lagaake (neeche dekho) in words se password variations bana sakta hai.

---

## CRUNCH — PATTERN-BASED WORDLIST GENERATE KARO

### Crunch Kya Hai?
Crunch specific patterns/rules se wordlist generate karta hai. Jab tujhe pata ho password
ka format (e.g., 4-digit PIN, company name + 2 digits), tab ye use karo.

### Basic Syntax:
```bash
crunch <min_length> <max_length> <charset> -o output.txt
```

### Symbol Reference (Pattern Placeholders):
| Symbol | Matlab | Example |
|---|---|---|
| `@` | Lowercase letter (a-z) | a, b, c, ... z |
| `,` | Uppercase letter (A-Z) | A, B, C, ... Z |
| `%` | Number (0-9) | 0, 1, 2, ... 9 |
| `^` | Special character (!@#$%^&*) | !, @, #, ... |

### Common Crunch Commands:

```bash
# 4-digit PINs (0000-9999):
crunch 4 4 0123456789 -o pins.txt
# Output: 10,000 combinations (0000, 0001, ... 9999)

# 6-char numeric passwords:
crunch 6 6 0123456789 -o six_digit.txt

# Company name + 2 digits (e.g., Mega00-Mega99):
crunch 6 6 -t Mega%% -o company_nums.txt
# Output: Mega00, Mega01, ... Mega99

# 2 letters + 4 digits (e.g., ab0000-zz9999):
crunch 6 6 -t @@%%%% -o alpha_num.txt

# Company + year pattern:
crunch 12 12 -t Company%%%% -o company_year.txt
# Output: Company0000, Company0001, ... Company9999

# Password with known prefix, unknown 3 chars:
crunch 10 10 -t Admin@@%%% -o admin_pass.txt
# Output: Adminaa000, Adminaa001, ... Adminzz999

# Only specific characters:
crunch 4 4 abc123 -o custom.txt
# Sirf a,b,c,1,2,3 se 4-char combinations

# WiFi passwords (8 char numeric):
crunch 8 8 0123456789 -o wifi_nums.txt
```

### File Size Warning:
```bash
# Crunch batata hai file kitni badi hogi:
crunch 8 8 0123456789
# Crunch will now generate the following amount of data: 900000000 bytes
# 858 MB — badi file! Soch ke run karo
```

---

## JOHN RULES — Existing Wordlist Mein Mutations Add Karo

### Rules Kya Hain?
John the Ripper rules wordlist ke har word mein modifications karta hai:
- password → Password, PASSWORD, password1, password!, p@ssword, etc.
- Ek word se dozens of variations ban jaati hain!

### Usage:
```bash
# Default rules (most common mutations):
john --wordlist=/usr/share/wordlists/rockyou.txt --rules hash.txt

# Specific ruleset:
john --wordlist=/usr/share/wordlists/rockyou.txt --rules=best64 hash.txt

# Hashcat mein rules:
hashcat -m MODE hash.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule
```

### Common Hashcat Rules Files:
```
/usr/share/hashcat/rules/best64.rule        ← Most effective 64 rules
/usr/share/hashcat/rules/rockyou-30000.rule ← Aggressive, 30k rules
/usr/share/hashcat/rules/d3ad0ne.rule       ← Classic ruleset
/usr/share/hashcat/rules/toggles1.rule      ← Case toggle variations
```

### Kya Rules Apply Hoti Hain (Examples):
```
Original: password
After rules:
  Password        ← First letter capitalize
  PASSWORD        ← All uppercase
  password1       ← Number append
  password!       ← Special char append
  password123     ← Multiple numbers
  p@ssword        ← Leet speak (a→@)
  pa$$word        ← Leet speak (ss→$$)
  drowssap        ← Reverse
  Password1!      ← Capitalize + number + special
```

### Kab Use Karo:
- rockyou.txt plain se crack nahi hua
- Password policy mein number/special char required hai (common mutation: word + 123 + !)
- CeWL output pe rules lagao (company words + variations)

---

## WORDLISTS COMBINE KARO

### Multiple Wordlists Merge:
```bash
# Simple merge:
cat wordlist1.txt wordlist2.txt > combined.txt

# Merge + remove duplicates:
cat wordlist1.txt wordlist2.txt | sort -u > combined.txt

# CeWL output + rockyou subset merge:
# Pehle rockyou se top 10000 lo:
head -10000 /usr/share/wordlists/rockyou.txt > top10k.txt
# CeWL output merge:
cat top10k.txt custom_cewl.txt | sort -u > final_wordlist.txt
```

### Username Wordlist Banao:
Agar employee names mile (LinkedIn, website se):
```bash
# Format: first.last, f.last, firstl, first
# John Smith → john.smith, j.smith, johns, john
# Tool: username-anarchy
username-anarchy --input-file fullnames.txt --select-format first.last > usernames.txt
```

---

## SECLISTS — Professional Wordlist Collection

### Install (Agar Nahi Hai):
```bash
sudo apt install seclists
# Location: /usr/share/wordlists/seclists/
```

### Important SecLists Paths:
```
/usr/share/wordlists/seclists/
  ├── Passwords/
  │   ├── Common-Credentials/
  │   │   ├── 10k-most-common.txt        ← Quick password list
  │   │   └── best1050.txt               ← Top 1050 passwords
  │   ├── Default-Credentials/           ← Default passwords for services
  │   └── Leaked-Databases/              ← Real leaked password lists
  ├── Discovery/
  │   ├── DNS/
  │   │   └── subdomains-top1million-5000.txt  ← Subdomain bruteforce
  │   ├── SNMP/
  │   │   └── common-snmp-community-strings.txt
  │   └── Web-Content/
  │       ├── common.txt
  │       └── big.txt
  └── Usernames/
      └── xato-net-10-million-usernames.txt  ← Username enumeration
```

---

## PRACTICAL SCENARIOS — Real Exam Situations

### Scenario 1: Hash Crack Karna Hai
```bash
# Step 1: Rockyou try karo
hashcat -m MODE hash.txt /usr/share/wordlists/rockyou.txt

# Step 2: Rockyou + rules
hashcat -m MODE hash.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule

# Step 3: CeWL + rules (agar target website hai)
cewl http://target -m 6 -w custom.txt
hashcat -m MODE hash.txt custom.txt -r /usr/share/hashcat/rules/best64.rule
```

### Scenario 2: Web Directory Bruteforce
```bash
# Step 1: common.txt (fast)
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt -x php,txt,html

# Step 2: Agar kuch nahi mila → medium
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

### Scenario 3: SSH Brute Force
```bash
# Username pata hai:
hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://<IP> -t 4

# Username nahi pata:
hydra -L /usr/share/wordlists/metasploit/unix_users.txt -P /usr/share/wordlists/seclists/Passwords/Common-Credentials/best1050.txt ssh://<IP> -t 4
```

### Scenario 4: WiFi Password Crack
```bash
aircrack-ng -w /usr/share/wordlists/rockyou.txt capture.cap
```

### Scenario 5: Known Pattern (e.g., "Password is 4-digit PIN")
```bash
crunch 4 4 0123456789 -o pins.txt
hashcat -m MODE hash.txt pins.txt
```

---

## Common Mistakes (Mat Karna Ye)

1. **rockyou.txt decompress nahi karna** — .gz file se crack nahi hoga, `gunzip` pehle
2. **Badi wordlist se shuru karna** — common.txt pehle, medium baad mein, big last mein
3. **Rules nahi lagana** — Plain rockyou fail ho toh rules add karo (best64.rule)
4. **CeWL bhool jaana** — Target website se custom words bohot effective hote hain
5. **Crunch se file size check nahi karna** — 8-char full charset = terabytes! Pattern specify karo
6. **Wrong wordlist for wrong task** — Password cracking mein common.txt nahi, directory bruteforce mein rockyou nahi
7. **Hashcat mein `-r` flag bhool jaana** — Rules file explicit specify karna padta hai
8. **SecLists install nahi hona** — `sudo apt install seclists` pehle check karo

---

## Quick Decision Tree

```
Kya karna hai?
  |
  +-- Password crack karna hai
  |     +-- rockyou.txt try karo (ALWAYS FIRST)
  |     +-- Nahi hua? --> rockyou + best64.rule
  |     +-- Nahi hua? --> CeWL se target words → rules ke saath
  |     +-- Pattern pata hai? --> Crunch se specific wordlist
  |     +-- Sab fail? --> rockyou + rockyou-30000.rule (aggressive)
  |
  +-- Directory bruteforce karna hai
  |     +-- common.txt (quick scan)
  |     +-- Nahi mila? --> directory-list-2.3-medium
  |     +-- Extensions add karo: -x php,html,txt,bak
  |
  +-- Username enumerate karna hai
  |     +-- unix_users.txt (Linux targets)
  |     +-- xato-net-10-million-usernames.txt (AD/large)
  |     +-- CeWL + username-anarchy (custom from website)
  |
  +-- Subdomain bruteforce karna hai
  |     +-- subdomains-top1million-5000.txt
  |
  +-- SNMP community string
  |     +-- common-snmp-community-strings.txt
  |
  +-- WiFi crack karna hai
  |     +-- rockyou.txt
  |     +-- Numeric only? --> crunch 8 8 0123456789
  |
  +-- Custom pattern chahiye
        +-- Crunch: crunch <min> <max> -t <pattern>
        +-- Symbols: @ = lowercase, , = uppercase, % = digit, ^ = special
```
