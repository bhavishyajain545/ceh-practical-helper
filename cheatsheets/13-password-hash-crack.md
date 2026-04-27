# PASSWORD HASH MILA — Crack Kaise Karu?

---

## Pehle Samjho: Hashing Kya Hai?

Hashing ek **one-way function** hai — matlab ek input ko ek fixed-length output mein convert karta hai,
lekin us output se wapas original input nikalna almost impossible hai.

**Example:** `password123` → MD5 hash → `482c811da5d5b4bc6d497ffa98491e38`

Ye "one-way" isliye hai kyunki hash se seedha password nahi nikal sakte.
Toh hum kya karte hain? **Hum bahut saare passwords ka hash banake compare karte hain** — isko bolte hain **dictionary attack / brute force**.

**Password storage mein use hota hai:**
- Linux: `/etc/shadow` file mein password hashes store hote hain
- Windows: SAM database mein NTLM hashes hote hain
- Web apps: Database mein MD5/SHA/bcrypt hashes

---

## STEP 1: Hash Type Identify Karo (Sabse Pehle Ye Karo!)

Hash ko dekhke type samajhna padega. Do tarike hain:
1. **Length aur format se manually identify karo**
2. **Tool use karo (hashid / hash-identifier)**

### Hash Types Table — Dhyan Se Dekho:

| Length / Format | Type | Example | Hashcat Mode (-m) | John Format (--format) |
|---|---|---|---|---|
| 32 hex chars | MD5 | `482c811da5d5b4bc6d497ffa98491e38` | 0 | raw-md5 |
| 40 hex chars | SHA-1 | `cbfdac6008f9cab4083784cbd1874f76618d2a97` | 100 | raw-sha1 |
| 64 hex chars | SHA-256 | `ef92b778bafe771e89245b89ecbc08a44a4e166c...` | 1400 | raw-sha256 |
| 128 hex chars | SHA-512 | `ba3253876aed6bc22d4a6ff53d8406c6ad864195...` | 1700 | raw-sha512 |
| 32 hex (Windows) | NTLM | `a4f49c406510bdcab6824ee7c30fd852` | 1000 | nt |
| `$1$salt$hash` | MD5-crypt (Linux) | `$1$abc$bM3R...` | 500 | md5crypt |
| `$5$salt$hash` | SHA-256-crypt | `$5$rounds=...` | 7400 | sha256crypt |
| `$6$salt$hash` | SHA-512-crypt | `$6$xyz$kL9p...` | 1800 | sha512crypt |
| `$2a$` ya `$2b$` | bcrypt | `$2a$10$N9qo8u...` | 3200 | bcrypt |
| `*` + 40 hex | MySQL 4.1+ | `*6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9` | 300 | mysql-sha1 |

### Kaise identify karein manually?
- **32 characters, sirf 0-9 a-f** → MD5 ya NTLM (context se samjho — Windows hai toh NTLM)
- **40 characters** → SHA-1
- **64 characters** → SHA-256
- **`$` se start ho raha hai** → Linux crypt format — `$1$` = MD5, `$5$` = SHA256, `$6$` = SHA512
- **`$2a$` ya `$2b$`** → bcrypt (bahut slow to crack — patience chahiye)

### Auto-Identify Tools:
```bash
# hashid — hash paste karo, ye type batayega
hashid '482c811da5d5b4bc6d497ffa98491e38'
# Output: [+] MD5, [+] NTLM ... (top match usually sahi hota hai)

# hash-identifier — interactive mode
hash-identifier
# Prompt aayega, hash paste karo, type batayega
```

> **Important:** hashid kabhi kabhi multiple possibilities deta hai.
> Context se decide karo — agar Windows machine se aaya hai toh NTLM, agar Linux se toh MD5-crypt, etc.

---

## STEP 2: Crack Karo — John ya Hashcat Se

### John the Ripper (Beginner Friendly)

John simple hai — hash file do, wordlist do, format batao, crack ho jayega.

```bash
# Step 1: Hash ko file mein daalo
echo "482c811da5d5b4bc6d497ffa98491e38" > hash.txt

# Step 2: John se crack karo
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
# --format = hash type (upar table se dekho)
# --wordlist = kaunsi wordlist use karni hai
# rockyou.txt = sabse famous wordlist (14 million passwords)

# Step 3: Result dekho
john --show --format=raw-md5 hash.txt
# Ye cracked password dikhayega
```

**Output kya aayega:**
```
Loaded 1 password hash (Raw-MD5 [MD5 256/256 AVX2 8x3])
password123      (?)
1g 0:00:00:01 DONE
```
Yahan `password123` crack hua password hai. `(?)` ka matlab user unknown hai.

### Hashcat (Fast — GPU use karta hai)

Hashcat zyada fast hai kyunki GPU use karta hai. Lekin syntax thoda different hai.

```bash
# Step 1: Hash file banao
echo "482c811da5d5b4bc6d497ffa98491e38" > hash.txt

# Step 2: Hashcat se crack karo
hashcat -m 0 hash.txt /usr/share/wordlists/rockyou.txt
# -m 0 = MD5 (mode number — upar table se dekho)
# Agar GPU nahi hai toh --force lagao

# Step 3: Result dekho
hashcat -m 0 hash.txt --show
# Output: 482c811da5d5b4bc6d497ffa98491e38:password123
```

> **John vs Hashcat kab use karein?**
> - John: Simple aur quick cracking ke liye, CPU-based
> - Hashcat: Bade wordlist ya complex hashes ke liye, GPU-based
> - CEH exam mein dono chalte hain — jo yaad ho wo use karo

---

## CASE 1: /etc/shadow File Mili (Linux Password Hashes)

Agar tumhe Linux machine ki `/etc/shadow` file mili hai, toh usme password hashes hain.

**Shadow file format samjho:**
```
root:$6$xyz$kL9pM3...:19000:0:99999:7:::
```
- `root` = username
- `$6$` = SHA-512-crypt type
- `xyz` = salt
- `kL9pM3...` = actual hash

**Crack kaise karein:**
```bash
# Step 1: passwd aur shadow file ko combine karo (unshadow)
# Ye isliye zaroori hai kyunki John ko username chahiye
unshadow passwd.txt shadow.txt > combined.txt

# Step 2: John se crack karo
john --wordlist=/usr/share/wordlists/rockyou.txt combined.txt
# John automatically detect kar lega ki SHA-512-crypt hai

# Step 3: Result dekho
john --show combined.txt
# Output: root:toor:0:0:root:/root:/bin/bash
# Yahan "toor" cracked password hai
```

> **Agar sirf shadow file hai (passwd nahi)?**
> Direct bhi crack ho sakta hai: `john --wordlist=rockyou.txt shadow.txt`
> Lekin unshadow karna better hai kyunki username mapping mil jaati hai.

---

## CASE 2: Windows NTLM Hash (SAM Dump / Hashdump)

Meterpreter ya mimikatz se hashes nikale hain toh ye format hoga:

```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:a4f49c406510bdcab6824ee7c30fd852:::
```
- Field 1: Username (`Administrator`)
- Field 2: RID (`500`)
- Field 3: LM Hash (usually empty/disabled — `aad3b435...` = empty)
- Field 4: **NTLM Hash** (ye crack karna hai!)

```bash
# Sirf NTLM hash extract karo (4th field)
# Agar file mein full hashdump format hai:
cut -d: -f4 hashdump.txt > ntlm_hashes.txt

# John se crack karo
john --format=nt --wordlist=/usr/share/wordlists/rockyou.txt ntlm_hashes.txt

# Ya Hashcat se
hashcat -m 1000 ntlm_hashes.txt /usr/share/wordlists/rockyou.txt

# Result dekho
john --show --format=nt ntlm_hashes.txt
```

> **NTLM hashes fast crack hote hain** kyunki ye unsalted hain (koi salt nahi lagta).
> Ek 8-char password seconds mein crack ho jaata hai.

---

## CASE 3: Protected Files — ZIP / RAR / PDF / SSH Key

Agar password-protected file mili hai toh pehle hash extract karna padta hai, phir crack karna padta hai.

```bash
# ZIP file ka hash nikalo
zip2john secret.zip > hash.txt
# Output mein ek lambi line aayegi — ye hash hai

# RAR file ka hash nikalo
rar2john archive.rar > hash.txt

# PDF file ka hash nikalo
pdf2john.pl file.pdf > hash.txt
# Note: pdf2john mein .pl extension hai (Perl script)

# SSH private key ka hash nikalo (passphrase-protected)
ssh2john.py id_rsa > hash.txt
# Note: ssh2john mein .py extension hai (Python script)

# Ab kisi bhi hash ko John se crack karo
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

# Result dekho
john --show hash.txt
```

**Output samjho (zip2john example):**
```
secret.zip/flag.txt:$pkzip2$...:secret.zip
```
John crack karega toh password dikhayega — us password se ZIP open kar lo.

> **Common galti:** `zip2john` ka output seedha file mein redirect karo,
> edit mat karo. John automatically samajh jayega.

---

## CASE 4: Rockyou Se Nahi Mila — Ab Kya Karein?

Kabhi kabhi rockyou.txt mein password nahi hota. Tab ye try karo:

### Option A: Website se custom wordlist banao (CeWL)
```bash
# Target website ke words se wordlist banao
cewl http://target-site.com -m 6 -w custom.txt
# -m 6 = minimum 6 character words
# -w custom.txt = output file

# Ab is wordlist se crack karo
john --wordlist=custom.txt hash.txt
```

### Option B: Pattern-based wordlist banao (Crunch)
```bash
# 4-digit PINs (0000-9999)
crunch 4 4 0123456789 -o pins.txt
# 4 4 = min length 4, max length 4

# Company name + numbers pattern
crunch 8 10 -t Company%% -o company_list.txt
# %% = 2 digits (00-99)

# Directly pipe to John (file save nahi karna)
crunch 4 4 0123456789 | john --stdin hash.txt
```

### Option C: John ko rules lagao (word variations)
```bash
# Rules matlab: password → Password, password1, PASSWORD, p@ssword, etc.
john --wordlist=rockyou.txt --rules hash.txt
# Ye rockyou ke har word ke variations try karega
```

> **Pro tip:** Agar question mein koi hint hai (company name, person name, etc.)
> toh pehle manually try karo, phir CeWL use karo.

---

## Common Mistakes (Mat Karna Ye)

1. **Hash type galat diya** → John/Hashcat wrong format mein crack karega = kuch nahi milega
   - Pehle hashid se confirm karo, phir crack karo

2. **rockyou.txt compressed hai** → File not found error aayega
   ```bash
   sudo gunzip /usr/share/wordlists/rockyou.txt.gz
   # Pehle extract karo, phir use karo
   ```

3. **NTLM aur MD5 confuse karna** → Dono 32 chars hain, lekin format different hai
   - Windows se aaya = NTLM (mode 1000)
   - Web app / general = MD5 (mode 0)

4. **Hash file mein extra spaces/newlines** → Crack fail ho jaata hai
   ```bash
   cat -A hash.txt   # $ signs extra newlines dikhayengi
   ```

5. **bcrypt crack karna with full rockyou** → Ghanton lag jayenge
   - bcrypt intentionally slow hai — chhoti wordlist use karo

6. **`john --show` kaam nahi kar raha** → `--format` bhi lagao show ke saath
   ```bash
   john --show --format=raw-md5 hash.txt
   ```

7. **Hashcat GPU error** → `--force` flag lagao (agar VM mein ho)
   ```bash
   hashcat -m 0 hash.txt rockyou.txt --force
   ```

---

## Quick Decision Tree

```
Hash mila — kya karu?
  │
  ├─ STEP 1: Type identify karo
  │   ├─ hashid '<HASH>' ya hash-identifier
  │   ├─ 32 chars? → MD5 (mode 0) ya NTLM (mode 1000)
  │   ├─ 40 chars? → SHA-1 (mode 100)
  │   ├─ 64 chars? → SHA-256 (mode 1400)
  │   ├─ $6$...? → SHA-512-crypt (mode 1800)
  │   └─ $2a$...? → bcrypt (mode 3200)
  │
  ├─ STEP 2: Source kya hai?
  │   ├─ /etc/shadow file? → unshadow → john
  │   ├─ Windows hashdump? → NTLM (4th field) → john --format=nt
  │   ├─ ZIP/RAR/PDF? → *2john → extract hash → john
  │   └─ Simple hash string? → john --format=X / hashcat -m X
  │
  ├─ STEP 3: Crack karo
  │   ├─ john --format=FORMAT --wordlist=rockyou.txt hash.txt
  │   └─ hashcat -m MODE hash.txt rockyou.txt
  │
  └─ STEP 4: Rockyou fail?
      ├─ cewl se custom wordlist banao
      ├─ crunch se pattern-based list banao
      └─ john --rules lagao (variations try karo)
```
