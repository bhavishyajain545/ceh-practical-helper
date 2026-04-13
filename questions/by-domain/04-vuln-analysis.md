# Question Bank — Vulnerability Analysis (12 questions)

> Realistic CEH-Practical-style vuln scanning questions. Steps link into individual tool docs — click any command to see the full reference.

**Legend:** 🟢 easy · 🟡 medium · 🔴 hard

---

### Q1 🟢 — Identify web server vulnerabilities on 10.10.10.5 with nikto

**Category:** Web vuln scan | **Tools:** [nikto](../../tools/nikto.md)

**Steps:**
1. `nikto -h http://10.10.10.5 -o nikto.txt` ← [why](../../tools/nikto.md#basic-usage)
2. Scroll the `+ OSVDB-xxxxx:` and `+ /path:` lines.

**Answer format:** OSVDB / CVE id, or the vulnerable path verbatim

**Gotcha:** nikto is loud. If the Q asks for "most critical", prioritize entries tagged `/admin`, `upload`, `shell`, or `backup`.

---

### Q2 🟢 — Find the CVE for vsftpd 2.3.4 on 10.10.10.5

**Category:** CVE lookup | **Tools:** [searchsploit](../../tools/searchsploit.md)

**Steps:**
1. Confirm version: `nmap -sV -p 21 10.10.10.5`
2. `searchsploit vsftpd 2.3.4` ← [why](../../tools/searchsploit.md#basic-usage)
3. Read the title — it names the CVE (backdoor command execution).

**Answer format:** `CVE-2011-2523` (or exploit path)

**Gotcha:** the vsftpd 2.3.4 backdoor doesn't have a "real" CVE that everyone agrees on — exam sometimes wants `OSVDB-73573` or the exploit path `unix/remote/17491.rb`.

---

### Q3 🟢 — Detect Heartbleed on 10.10.10.10:443

**Category:** SSL vuln | **Tools:** [nmap NSE](../../tools/nmap.md#nse-scripts-the-money-section)

**Steps:**
1. `nmap -p 443 --script ssl-heartbleed 10.10.10.10` ← [why](../../tools/nmap.md#nse-scripts-the-money-section)
2. Look for `VULNERABLE: The Heartbleed Bug`.

**Answer format:** `CVE-2014-0160` / Yes

**Gotcha:** Heartbleed only affects OpenSSL `1.0.1` through `1.0.1f` — if nmap reports `not vulnerable`, confirm the version with `--script ssl-enum-ciphers`.

---

### Q4 🟢 — Detect weak SSL/TLS ciphers on 10.10.10.10:443

**Category:** SSL vuln | **Tools:** [nmap NSE](../../tools/nmap.md#nse-scripts-the-money-section), [sslscan](../../tools/sslscan.md)

**Steps:**
1. `nmap -p 443 --script ssl-enum-ciphers 10.10.10.10` ← [why](../../tools/nmap.md#nse-scripts-the-money-section)
2. Or faster: `sslscan 10.10.10.10:443` ← [why](../../tools/sslscan.md#basic-usage)
3. Look for `grade: F`, `SSLv2`, `SSLv3`, `RC4`, `EXPORT`, `NULL`.

**Answer format:** cipher name or protocol (e.g. `SSLv3` or `TLS_RSA_WITH_RC4_128_SHA`)

---

### Q5 🟡 — Find an outdated Apache module on 10.10.10.15

**Category:** Banner / CVE | **Tools:** [nikto](../../tools/nikto.md), `curl`

**Steps:**
1. Banner: `curl -sI http://10.10.10.15 | grep -i server`
2. `nikto -h http://10.10.10.15` — look for `Apache/... appears to be outdated` and `mod_*`
3. Cross-reference module version in [searchsploit](../../tools/searchsploit.md): `searchsploit apache mod_ssl 2.8`

**Answer format:** module name + version (e.g. `mod_ssl 2.8.7`)

**Gotcha:** `ServerTokens Prod` hides module versions — fall back to `OPTIONS /` or default error pages.

---

### Q6 🟡 — Find the CVE for Samba 3.0.20 on 10.10.10.20

**Category:** CVE lookup | **Tools:** [searchsploit](../../tools/searchsploit.md), [nmap NSE](../../tools/nmap.md#nse-scripts-the-money-section)

**Steps:**
1. Confirm version: `nmap -sV -p 139,445 10.10.10.20`
2. `searchsploit samba 3.0.20` ← [why](../../tools/searchsploit.md#basic-usage)
3. The hit is "Samba 3.0.20 < 3.0.25rc3 — Username map script" → CVE-2007-2447.

**Answer format:** `CVE-2007-2447`

---

### Q7 🟡 — Identify EternalBlue vulnerability on 10.10.10.25

**Category:** SMB vuln | **Tools:** [nmap NSE](../../tools/nmap.md#nse-scripts-the-money-section)

**Steps:**
1. `nmap -p 445 --script smb-vuln-ms17-010 10.10.10.25` ← [why](../../tools/nmap.md#nse-scripts-the-money-section)
2. Look for `VULNERABLE: Remote Code Execution vulnerability`.

**Answer format:** `MS17-010` / `CVE-2017-0144`

**Gotcha:** exam may accept the friendly name `EternalBlue`. Note all three.

---

### Q8 🟡 — Scan 10.10.10.30 for a WordPress vulnerable plugin

**Category:** CMS vuln | **Tools:** [wpscan](../../tools/wpscan.md)

**Steps:**
1. `wpscan --url http://10.10.10.30 --enumerate vp,vt,u` ← [why vp](../../tools/wpscan.md#enumerate)
2. Read the `[!] Title:` lines under `[+] WordPress theme/plugin in use`.
3. Optional API token: `--api-token <KEY>` — adds CVE info.

**Answer format:** plugin name + version (e.g. `revslider 4.1.4`) or CVE

**Gotcha:** `vp` = vulnerable plugins, `ap` = all plugins; `ap` is much louder and slower.

---

### Q9 🟡 — Identify default credentials on 10.10.10.40

**Category:** Auth vuln | **Tools:** [nmap NSE](../../tools/nmap.md#nse-scripts-the-money-section), [hydra](../../tools/hydra.md)

**Steps:**
1. Find the service: `nmap -sV 10.10.10.40`
2. Check common defaults: `tomcat/tomcat`, `admin/admin`, `root/root`, `admin/password`.
3. NSE: `nmap -p 80 --script http-default-accounts 10.10.10.40` ← [why](../../tools/nmap.md#nse-scripts-the-money-section)
4. Brute: `hydra -L users.txt -P passwords.txt 10.10.10.40 http-get /manager/html`

**Answer format:** `username:password`

**Gotcha:** Tomcat Manager returns 401 — hydra module is `http-get` with the path.

---

### Q10 🔴 — Find an exploit for an obscure service banner

**Category:** Exploit search | **Tools:** [searchsploit](../../tools/searchsploit.md)

**Steps:**
1. Get version: `nmap -sV -p <port> 10.10.10.50`
2. `searchsploit <product> <version>` ← [why](../../tools/searchsploit.md#basic-usage)
3. If no hit, drop version: `searchsploit <product>`
4. Mirror the exploit locally: `searchsploit -m <ID>` ← [why -m](../../tools/searchsploit.md#mirror)
5. Read the header comments for CVE + usage.

**Answer format:** CVE, exploit ID, or file path (e.g. `exploits/linux/remote/42315.py`)

**Gotcha:** searchsploit has false positives — confirm the banner matches the exploit's `Tested on:` line.

---

### Q11 🔴 — Run a full nmap vuln-category sweep against 10.10.10.60

**Category:** Batch vuln scan | **Tools:** [nmap NSE](../../tools/nmap.md#nse-scripts-the-money-section)

**Steps:**
1. `nmap -sV --script vuln 10.10.10.60 -oN vuln.txt` ← [why vuln category](../../tools/nmap.md#nse-scripts-the-money-section)
2. `grep -i "VULNERABLE" vuln.txt`
3. For noise reduction: `--script "vuln and not http-*"` to skip slow web scripts.

**Answer format:** the script name and/or CVE printed (e.g. `smb-vuln-ms17-010 / CVE-2017-0144`)

**Gotcha:** `--script vuln` is slow — scope it to relevant ports (`-p 445,80,443`) instead of `-p-`.

---

### Q12 🔴 — Identify a vulnerable database server on 10.10.10.70

**Category:** DB vuln | **Tools:** [nmap NSE](../../tools/nmap.md#nse-scripts-the-money-section), [searchsploit](../../tools/searchsploit.md)

**Steps:**
1. Identify: `nmap -sV -p 1433,3306,5432,1521,27017 10.10.10.70`
2. MySQL: `nmap -p 3306 --script "mysql-* and not brute" 10.10.10.70`
3. MSSQL: `nmap -p 1433 --script "ms-sql-info,ms-sql-empty-password" 10.10.10.70`
4. Cross-check version: `searchsploit mysql 5.1`

**Answer format:** CVE, empty-password account, or vulnerable version string

**Gotcha:** MSSQL default `sa` with empty password is one of the most-tested exam items — always run `ms-sql-empty-password`.

---

## Quick links

- [nikto reference](../../tools/nikto.md)
- [nmap NSE reference](../../tools/nmap.md#nse-scripts-the-money-section)
- [searchsploit reference](../../tools/searchsploit.md)
- [wpscan reference](../../tools/wpscan.md)
- [Vuln analysis playbook (decision tree)](../../playbooks/vuln-analysis-playbook.md)
- [04-vuln-analysis README](../../04-vuln-analysis/README.md)
- [Previous: 03-enumeration](03-enumeration.md) · [Next: 11-web-servers](11-web-servers.md)
