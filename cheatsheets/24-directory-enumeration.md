# 🔍 HIDDEN DIRECTORIES / FILES DHUNDO

---

## CASE 1: Gobuster (Fastest)
```bash
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt -t 30
gobuster dir -u http://<IP> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 30 -x php,html,txt
```

## CASE 2: Dirb
```bash
dirb http://<IP>
dirb http://<IP> /usr/share/wordlists/dirb/big.txt
```

## CASE 3: Ffuf (Flexible)
```bash
ffuf -u http://<IP>/FUZZ -w /usr/share/wordlists/dirb/common.txt
ffuf -u http://<IP>/FUZZ -w wordlist.txt -fc 404          # filter 404
ffuf -u http://<IP>/FUZZ -w wordlist.txt -mc 200,301      # match codes
```

## CASE 4: Nikto (Vuln Scanner + Dirs)
```bash
nikto -h http://<IP>
```

---

## Common Interesting Finds:
| Path | Why Important |
|---|---|
| `/admin/` | Admin panel |
| `/login.php` | Login page → brute force / SQLi |
| `/phpmyadmin/` | Database access |
| `/robots.txt` | Hidden paths |
| `/.git/` | Source code leak |
| `/backup/` | Backup files |
| `/config.php` | Config with creds |
| `/wp-admin/` | WordPress admin |
| `/shell.php` | Already uploaded shell? |

---

## QUICK DECISION:
```
Web server mila
  ├─ Quick scan: gobuster + common.txt
  ├─ Thorough: gobuster + medium.txt + -x php,html,txt
  ├─ robots.txt manually check karo
  ├─ Nikto for known vulnerabilities
  └─ Interesting path mili → investigate / exploit
```
