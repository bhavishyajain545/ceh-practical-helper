# Question Bank — Scanning (12 questions)

> Realistic CEH-Practical-style questions. Steps link into the [nmap tool doc](../../tools/nmap.md) — click any flag to read what it does.

**Legend:** 🟢 easy · 🟡 medium · 🔴 hard

---

### Q1 🟢 — Find the FTP server version on 10.10.10.5

**Category:** Version detection | **Tools:** [nmap](../../tools/nmap.md)

**Steps:**
1. `nmap -sV -p 21 10.10.10.5` ← [why -sV](../../tools/nmap.md#scan-techniques)
2. Read the `VERSION` column.

**Answer format:** `<software> <version>` exactly as printed (e.g. `vsftpd 2.3.4`)

**Gotcha:** if `filtered`, add `-Pn`.

---

### Q2 🟢 — How many live hosts in the 10.10.10.0/24 subnet?

**Category:** Host discovery | **Tools:** [nmap](../../tools/nmap.md)

**Steps:**
1. `nmap -sn 10.10.10.0/24 | grep -c "Host is up"` ← [why -sn](../../tools/nmap.md#host-discovery)

**Answer format:** integer

**Gotcha:** if ICMP blocked, use `sudo nmap -PR 10.10.10.0/24` (ARP — LAN only) or `nmap -PS80,443 10.10.10.0/24`.

---

### Q3 🟢 — How many open TCP ports on 10.10.10.20?

**Category:** Port scan | **Tools:** [nmap](../../tools/nmap.md)

**Steps:**
1. `nmap -p- -T4 10.10.10.20 -oN ports.txt` ← [why -p-](../../tools/nmap.md#port-specification)
2. `grep -c "^[0-9].*open" ports.txt`

**Answer format:** integer

---

### Q4 🟡 — Identify the OS running on 10.10.10.15

**Category:** OS detection | **Tools:** [nmap](../../tools/nmap.md)

**Steps:**
1. `sudo nmap -O 10.10.10.15` ← needs root, [why](../../tools/nmap.md#scan-techniques)
2. Look for `OS details:` line.
3. If unsure, also run `-sV` — versions sometimes reveal Windows build (e.g. `Microsoft Windows Server 2008`).

**Answer format:** OS name + version (e.g. `Windows 7 Professional`)

---

### Q5 🟡 — Find the vulnerable Windows SMB service on 10.10.10.7 (CVE)

**Category:** Vuln scan | **Tools:** [nmap NSE](../../tools/nmap.md#nse-scripts-the-money-section)

**Steps:**
1. Confirm SMB open: `nmap -p 445 10.10.10.7`
2. Run vuln scripts: `nmap -p 445 --script "smb-vuln-*" 10.10.10.7` ← [why these scripts](../../tools/nmap.md#nse-scripts-the-money-section)
3. Read for `VULNERABLE:` lines.

**Answer format:** CVE or MS-ID (e.g. `MS17-010` or `CVE-2017-0144`)

**Gotcha:** the answer they want might be the script name (`smb-vuln-ms17-010`), the CVE, or the friendly name (`EternalBlue`). Note all three.

---

### Q6 🟡 — Determine the SNMP community string on 10.10.10.30

**Category:** UDP enum | **Tools:** [nmap NSE](../../tools/nmap.md#nse-scripts-the-money-section)

**Steps:**
1. Confirm SNMP open: `sudo nmap -sU -p 161 10.10.10.30`
2. Brute-force community: `sudo nmap -sU -p 161 --script snmp-brute 10.10.10.30`
3. Look for `valid-communities` in output.

**Answer format:** the string (often `public` or `private`)

**Gotcha:** UDP scans are slow — limit to `-p 161`, never `-sU` without ports.

---

### Q7 🟢 — What is the title of the web page on 10.10.10.40?

**Category:** HTTP enum | **Tools:** [nmap NSE](../../tools/nmap.md#nse-scripts-the-money-section)

**Steps:**
1. `nmap -p 80,443 --script http-title 10.10.10.40`
2. Read `|_http-title:` line.

**Answer format:** the page title verbatim.

---

### Q8 🟡 — Is anonymous FTP login allowed on 10.10.10.5?

**Category:** Service config | **Tools:** [nmap NSE](../../tools/nmap.md#nse-scripts-the-money-section)

**Steps:**
1. `nmap -p 21 --script ftp-anon 10.10.10.5`
2. Look for `Anonymous FTP login allowed`.

**Answer format:** Yes / No (and possibly list of files if asked)

---

### Q9 🔴 — Perform a DNS zone transfer against ns1.example.com (10.10.10.50)

**Category:** DNS enum | **Tools:** [nmap NSE](../../tools/nmap.md#nse-scripts-the-money-section), `dig`

**Steps:**
1. nmap method:
   ```bash
   nmap -p 53 --script dns-zone-transfer \
        --script-args dns-zone-transfer.domain=example.com 10.10.10.50
   ```
2. dig method (often easier):
   ```bash
   dig axfr example.com @10.10.10.50
   ```
3. Count records or list subdomains as the question asks.

**Answer format:** depends on Q — often a subdomain or a record count.

**Gotcha:** AXFR usually fails on real targets — if it works, you've found the intended path.

---

### Q10 🟡 — Find the SSL certificate's Common Name on 10.10.10.60:443

**Category:** TLS recon | **Tools:** [nmap NSE](../../tools/nmap.md#nse-scripts-the-money-section), `openssl`

**Steps:**
1. `nmap -p 443 --script ssl-cert 10.10.10.60`
2. Look for `Subject: commonName=...`
3. Or with openssl:
   ```bash
   echo | openssl s_client -connect 10.10.10.60:443 2>/dev/null \
     | openssl x509 -noout -subject
   ```

**Answer format:** CN value (e.g. `secure.example.com`)

---

### Q11 🔴 — Identify all hosts running a web server in 10.10.10.0/24

**Category:** Service sweep | **Tools:** [nmap](../../tools/nmap.md)

**Steps:**
1. Scan the whole subnet for HTTP ports:
   ```bash
   nmap -p 80,443,8080,8443 --open 10.10.10.0/24 -oN web.txt
   ```
2. List unique IPs:
   ```bash
   grep "Nmap scan report" web.txt
   ```

**Answer format:** count or list of IPs

---

### Q12 🔴 — Bypass firewall to scan a host that drops SYN packets

**Category:** Evasion | **Tools:** [nmap](../../tools/nmap.md), `hping3`

**Steps:**
1. Try ACK scan (maps firewall rules):
   ```bash
   sudo nmap -sA -p- 10.10.10.80
   ```
2. Try fragmented SYN:
   ```bash
   sudo nmap -sS -f -Pn 10.10.10.80
   ```
3. Try a different source port (firewalls often allow port 53):
   ```bash
   sudo nmap -sS -Pn --source-port 53 10.10.10.80
   ```
4. Try idle scan if there's a zombie:
   ```bash
   sudo nmap -sI <ZOMBIE_IP> 10.10.10.80
   ```

**Answer format:** depends — usually a port list or a vuln found post-bypass.

**Gotcha:** these are the "hard" tail of CEH scanning Qs — try them in this order.

---

## 📌 Quick links

- [nmap full reference](../../tools/nmap.md)
- [Scanning playbook (decision tree)](../../playbooks/scanning-playbook.md)
- [02-scanning README](../../02-scanning/README.md)
