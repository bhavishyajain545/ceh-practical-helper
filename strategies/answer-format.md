# 🎯 Answer Format — Yahan Marks Marte Hain

> Sabse zyada lost marks galat answer se nahi, **galat format se** hote hain. Yeh chapter sabse important hai.

## The cruel truth

CEH grading is **strict**. Answer correct hai but format galat = **0 marks**.

Examples of format failures:
- Question wants `vsftpd 2.3.4`, you wrote `vsftpd2.3.4` → **wrong**
- Question wants `Microsoft Windows 7` you wrote `Windows 7` → **wrong**
- Question wants integer `5`, you wrote `5 hosts` → **wrong**
- Question wants CVE `CVE-2017-0144`, you wrote `2017-0144` → **wrong**
- Trailing space, capitalization, hyphen vs underscore, dot at end → all can fail

**Lesson:** Always copy verbatim from tool output OR exact format the question states.

---

## Format types — har question yeh expect kar sakta hai

### 1. Verbatim from tool
- Service version: `vsftpd 2.3.4` (exactly as `nmap -sV` printed)
- OS: `Microsoft Windows Server 2008 R2 SP1` (verbatim from `nmap -O`)
- Banner: copy entire line

**Rule:** Tool jo print kare, **wahi paste karo** — kuch add/remove mat karo.

### 2. Integers
- Number of hosts: `5`
- Number of ports: `12`
- Number of users: `8`

**Rule:** Sirf number. No "hosts", "ports", "users", no commas, no spaces.

### 3. CVE format
- Always: `CVE-YYYY-NNNNN`
- Example: `CVE-2017-0144`

**Wrong forms to avoid:**
- `2017-0144` (missing CVE prefix)
- `CVE 2017 0144` (spaces instead of dashes)
- `cve-2017-0144` (lowercase — usually fails)

### 4. MS Bulletin format
- `MS17-010`, `MS08-067`
- All caps, hyphen between

### 5. Hostname / FQDN
- FQDN: `mail.example.com`
- Short: `MAIL` or `mail` (read question carefully)
- NetBIOS: usually `UPPERCASE`

### 6. IP address
- IPv4: `192.168.1.1` (dot-decimal)
- IPv6: `fe80::1` (compressed)
- CIDR: `10.10.10.0/24`

### 7. Hash
- MD5: 32 hex chars, lowercase usually
- SHA1: 40 hex chars
- NTLM: 32 hex chars
- Always copy entire string, no leading/trailing whitespace

### 8. Path / filename
- Linux: `/etc/passwd`, `/var/www/html/index.php`
- Windows: `C:\Windows\System32\config\SAM`
- Read question for slash direction

### 9. Username / password
- Plaintext as found
- Case sensitive!
- Surrounding quotes only if question asks

### 10. Flag / answer string
- Often `flag{...}` or `FLAG{...}` or just plaintext
- Copy exactly including braces if format shown

---

## The 3-step verify

Before submitting EVERY answer:

### Step 1: Question dubara padho
"Format kya maanga hai?" Note in mind.

### Step 2: Format match check
Apna answer aur question's format requirement compare karo:
- Type match? (number vs string)
- Case match? (upper/lower)
- Punctuation match? (dashes, dots, slashes)
- Whitespace? (no leading/trailing)

### Step 3: Verbatim check
Agar tool output se aaya hai, **literally copy-paste**, retype nahi karo.

---

## Common format traps

### Trap 1: "Vendor product version"
Format examples:
- `vsftpd 2.3.4` — space separator
- `vsftpd_2.3.4` — underscore (rare)
- `vsftpd-2.3.4` — dash (rare)

Question dekho — usually space. But if example given, follow exact.

### Trap 2: Windows OS naming
- `Microsoft Windows 7` vs `Windows 7` vs `Windows 7 Professional`
- `Microsoft Windows Server 2008 R2` vs `Windows 2008`

**Always use the longest, most specific name from `nmap -O` output.**

### Trap 3: "Number of"
- "Number of open ports" → `12` not `12 ports` or `twelve`
- "Number of users" → `8` not `8 users`

### Trap 4: Domain user format
- `DOMAIN\username` (Windows backslash)
- `username@DOMAIN.local` (UPN)
- `username` (just user)

Read carefully which format.

### Trap 5: Email vs username
- "Email of admin": `admin@target.com`
- "Username of admin": `admin`

### Trap 6: Hash output mein extra info
hashcat sometimes outputs:
```
8846f7eaee8fb117ad06bdd830b7586c:Password123
```
Question wants: hash? plaintext? both?

Read carefully — answer usually wants the **plaintext** (after the colon).

### Trap 7: Trailing newline
When you `cat` a file or copy from terminal, sometimes a `\n` sneaks in. Verify with:
```bash
echo -n "Password123" | wc -c
```
Should be exactly the visible character count.

---

## Common mistakes that cost marks

| Mistake | Example | Fix |
|---|---|---|
| Trailing space | `vsftpd 2.3.4 ` | Trim |
| Wrong case | `cve-2017-0144` | Use `CVE-2017-0144` |
| Added units | `5 hosts` | Just `5` |
| Wrong separator | `vsftpd-2.3.4` | Usually space |
| Wrong direction slashes | `C:/Windows/SAM` | Windows uses `\` |
| Quote marks | `"vsftpd 2.3.4"` | No quotes unless asked |
| Dot at end | `vsftpd 2.3.4.` | No |
| Extra newline | (invisible `\n`) | Use `echo -n` to verify |

---

## Tool-specific format tips

### nmap
- Version string: copy from VERSION column verbatim, including space
- OS: from "OS details:" line, full string
- Port count: count "open" lines

### enum4linux / smbclient
- Usernames: usually lowercase as printed
- Share names: case-preserved, no leading slash

### sqlmap
- DB names, table names, column names: copy exactly
- Don't prepend "database " or anything

### hashcat
- Cracked password: copy text after the colon, nothing else
- `hashcat --show` is cleaner output

### john
- Cracked password: copy second column from `john --show`

### exiftool
- Tag values: usually copy exactly, including dates in tool's format

---

## Format verification command examples

```bash
# Strip trailing whitespace from anything
echo "vsftpd 2.3.4 " | sed 's/[[:space:]]*$//'

# Get exact length (spot extra chars)
echo -n "Password123" | wc -c

# View hidden chars
echo "Password123" | cat -A

# Strip newlines
echo "Password123" | tr -d '\n'
```

---

## Final advice: when in doubt

**Read the question 4 times** if needed. Format galti se manhua nahi hota — manhua hota hai jab tum jaldi mein submit karte ho.

> **"Format galti = pure question waste. 5 sec ka verify saves 1 mark."**

Ek mark = ek question. 14 chahiye. Don't lose any to format.
