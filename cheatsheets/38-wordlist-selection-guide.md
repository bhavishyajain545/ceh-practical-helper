# 📝 KAUNSI WORDLIST USE KARU?

---

## Quick Selection

| Situation | Wordlist | Path |
|---|---|---|
| **Default** (almost always start here) | rockyou.txt | `/usr/share/wordlists/rockyou.txt` |
| **Directory bruteforce** | common.txt | `/usr/share/wordlists/dirb/common.txt` |
| **Directory (thorough)** | medium list | `/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt` |
| **Usernames** | unix_users | `/usr/share/wordlists/metasploit/unix_users.txt` |
| **SNMP communities** | snmp communities | `/usr/share/wordlists/seclists/Discovery/SNMP/common-snmp-community-strings.txt` |
| **DNS subdomains** | subdomains | `/usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-5000.txt` |
| **Custom (target-specific)** | CeWL generated | `cewl http://target -m 6 -w custom.txt` |
| **PIN/Pattern** | Crunch generated | `crunch 4 4 0123456789 -o pins.txt` |

---

## Rockyou Nahi Mila / Compressed?
```bash
# Decompress
sudo gunzip /usr/share/wordlists/rockyou.txt.gz

# Verify
wc -l /usr/share/wordlists/rockyou.txt
# Should be ~14 million lines
```

---

## CeWL — Target-Specific Wordlist
```bash
cewl http://<IP> -m 6 -w target_words.txt
# Company name, product names, employee names → passwords mein use hote hain
```

---

## Crunch — Pattern-Based
```bash
crunch 4 4 0123456789 -o pins.txt                    # 4-digit PINs
crunch 8 8 -t Company%% -o company_nums.txt          # Company + 2 digits
crunch 6 6 -t @@%%%% -o alpha_nums.txt               # 2 letters + 4 digits
```

---

## QUICK DECISION:
```
Password cracking
  ├─ Start with rockyou.txt (always)
  ├─ Rockyou fail?
  │   ├─ CeWL: spider target website for custom words
  │   ├─ Crunch: known pattern (PINs, company+digits)
  │   └─ John --rules: add mutations to rockyou
  ├─ Directory bruteforce → dirb/common.txt
  └─ Subdomain → seclists/DNS/subdomains
```
