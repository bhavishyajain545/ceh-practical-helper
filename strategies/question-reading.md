# 📖 Question Kaise Padho — Hidden Clues

> 80% of "stuck" moments hote hain question galat samajhne se. Yeh skill sabse undertaught hai.

## Rule #1: Question 2 baar padho

Pehli baar fast — overall idea ke liye.
Doosri baar slow — har word note karke.

**Kyun?** CEH questions mein har word matter karta hai. "Find the version" vs "Find the vulnerability" — completely different commands.

---

## Rule #2: 5 things har question se nikaalo

Har question padhke ye 5 cheezein notepad mein likho:

```
Q3:
TARGET:    10.10.10.5
ASK:       FTP server version
DOMAIN:    Scanning (02)
TOOL:      nmap -sV
FORMAT:    "<software> <version>" (e.g. "vsftpd 2.3.4")
```

In 5 lines mein **80% question solve ho gaya** before tum command bhi chalao.

---

## Keywords aur woh kya bolte hain

| Keyword in question | Yeh implies |
|---|---|
| "version" | `nmap -sV` ya `whatweb` |
| "vulnerability" / "CVE" | `nmap --script vuln` ya `searchsploit` |
| "open ports" | `nmap -p-` |
| "live hosts" | `nmap -sn` |
| "share" | SMB → `enum4linux` ya `smbclient -L` |
| "user" / "users" | enum4linux, snmpwalk, ldapsearch |
| "password" / "credentials" | hash crack ya brute force |
| "hash" | hashid + john/hashcat |
| "directory" / "admin panel" | gobuster ya ffuf |
| "database" / "SQL" | sqlmap |
| "robots.txt" | curl http://target/robots.txt |
| "wordpress" | wpscan |
| "wifi" / "WPA" / "ESSID" | aircrack suite |
| "pcap" / "capture" | wireshark |
| "image" / "hidden" / "stego" | exiftool → binwalk → steghide |
| "bucket" / "S3" | aws cli |
| "APK" / "android" | apktool / jadx |
| "decode" / "encode" | base64, hex, CyberChef |
| "encrypt" / "decrypt" | openssl |
| "metadata" | exiftool |
| "default credentials" | cirt.net default-creds list |

---

## Format clues hidden in question

Question kabhi-kabhi format ka hint deta hai:

| Question phrase | Format expected |
|---|---|
| "What is the version of..." | exact string from tool output |
| "How many..." | integer (no units, no commas) |
| "Identify the CVE..." | `CVE-XXXX-XXXXX` |
| "What is the name of..." | exact name as stored |
| "What is the password of..." | plaintext, case-sensitive |
| "What is the hash of..." | full hash string |
| "What flag did you find..." | exact flag string (often `flag{...}` or `FLAG{...}`) |
| "List all..." | comma-separated values |
| "What hostname..." | FQDN or short name (read carefully) |

**Pro tip:** Agar question mein example format diya hai (e.g. "Answer in format `vendor_product`"), **strictly** uska use karo. Underscore vs space vs dash matter karta hai.

---

## "Trick" words to watch for

Yeh words tumhe rok ke sochna chahiye:

### "Internal" / "external"
- Internal = tum target ke andar ho already (post-exploitation)
- External = tum target ke bahar ho (initial access)

### "Latest" / "first" / "oldest"
- Time-based filter — tool output mein dates check karo
- E.g. "First user created" → look at last column of `enum4linux -U`

### "Highest privilege"
- Usually root / Administrator / Domain Admin
- Look for `uid=0` or `S-1-5-21-...-500`

### "Default"
- Default credentials, default port, default config
- Use cirt.net or [tools/default-creds.md](../tools/default-creds.md)

### "Hidden"
- Stego, .htaccess, .git/, robots.txt disallow
- Or hidden SSID (`airodump` shows it as `<length: X>`)

### "All"
- Don't stop at first finding
- "List ALL users" → enumerate fully
- "ALL open ports" → use `-p-`, not `--top-1000`

### "Specific"
- Filter heavily — narrow down
- "Specific CVE for vsftpd 2.3.4" → not just "any vsftpd CVE"

---

## Numerical hints

Kabhi kabhi question ek number deta hai jo important hai:

| Number in question | Possible meaning |
|---|---|
| `10.10.10.X` | Target IP (most obvious) |
| `2.4.49` | Software version → CVE lookup |
| `21`, `80`, `443`, `445` | Port number → service |
| `64`, `128`, `256` | Bit length → encryption strength |
| `MS17-010`, `CVE-2017-0144` | Specific vuln to confirm |

---

## Pattern: question ka "shape"

Questions usually 4 shapes mein aate hain:

### Shape A: "Find X"
"Find the version of FTP on 10.10.10.5"
- Single tool, single command, single answer.
- 🟢 easy. 5 min.

### Shape B: "Exploit X to get Y"
"Exploit the SMB vulnerability to retrieve the flag from C:\flag.txt"
- Multi-step. Find → exploit → post-exploit → answer.
- 🟡 medium. 15-25 min.

### Shape C: "Crack X"
"Crack the hash and find the password for user john"
- Hash given OR find hash → identify type → crack → answer.
- 🟡 medium. 10-20 min depending on hash type.

### Shape D: "Identify X using Y output"
"Analyze the pcap and find the password used in the FTP login"
- File given (pcap, image, APK, hash) → analyze → extract.
- 🟢 to 🔴 depending on file complexity.

**Shape recognize hote hi mental model load ho jata hai.**

---

## Rule #3: Answer format ko question complete hote hi note karo

Tumne abhi command bhi nahi chalayi, lekin format pehle hi note karo. Kyun?

Kabhi kabhi tool 5 cheezein print karega aur tumhe samajh nahi aayega kaun-si daalni hai. Format pre-noted ho toh decision easy:
- Format = "version" → `vsftpd 2.3.4` line dhundo
- Format = "CVE" → output mein `CVE-` line dhundo
- Format = "integer" → count karo

---

## Anti-pattern: "I'll figure out the answer format from the tool output"

GALAT. Format pehle decide karo, phir output usme fit karo.

Example:
- Question: "What is the OS version of 10.10.10.20?"
- Tool: `nmap -O 10.10.10.20`
- Output: 6 different OS guesses with confidence scores
- **If you didn't pre-note format**, tum confused ho jaoge
- **Format pre-noted (`Windows X Y`)**, tum sirf woh line dhundoge

---

## Practice drill (do this in prep)

Kal ke 5 questions le kar:
1. Pehle question 2 baar padho
2. 5 things write down (target, ask, domain, tool, format)
3. Tab command run karo
4. Dekh ki tum kitne sahi guess kiye

Yeh 1 hafte mein muscle memory ban jayega.
