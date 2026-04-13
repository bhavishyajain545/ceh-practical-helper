# 04 Vulnerability Analysis — full walkthroughs

End-to-end examples. Input is usually a banner or an IP. Output is a CVE, CVSS, or exploit name.

---

## Walkthrough 1: "What CVE affects the FTP server on 10.10.10.5?"

**Type:** banner → CVE | **Difficulty:** easy | **Time:** 2 min

1. Get the banner first ([why -sV](../tools/nmap.md#scan-techniques)):
   ```bash
   nmap -sV -p 21 10.10.10.5
   ```
   Output:
   ```
   21/tcp open  ftp  vsftpd 2.3.4
   ```
2. Feed it to searchsploit with `-w` for CVE links:
   ```bash
   searchsploit -w vsftpd 2.3.4
   ```
3. Example output:
   ```
   vsftpd 2.3.4 - Backdoor Command Execution | https://www.exploit-db.com/exploits/49757
   ```
4. Click the URL (or Google `vsftpd 2.3.4 CVE`) → `CVE-2011-2523`.
5. **Answer:** `CVE-2011-2523`

**Gotcha:**
- CEH sometimes wants the **exploit name** (`vsftpd 2.3.4 backdoor`) not the CVE — read the question wording.
- Don't trust searchsploit's first hit blindly — multiple `2.3.x` entries exist.

---

## Walkthrough 2: "Find all web vulnerabilities on http://10.10.10.6"

**Type:** web vuln scan | **Difficulty:** easy | **Time:** 3–5 min

1. Fire nikto:
   ```bash
   nikto -h http://10.10.10.6 -o nikto.txt
   ```
2. While it runs, start an NSE vuln scan in another pane:
   ```bash
   nmap -p 80 --script "http-vuln-*,http-enum" 10.10.10.6
   ```
3. Read nikto's output — key markers:
   ```
   + Server: Apache/2.2.8 (Ubuntu) DAV/2
   + OSVDB-877: HTTP TRACE method is active
   + /phpinfo.php: Output from the phpinfo() function was found.
   + Apache/2.2.8 appears to be outdated
   ```
4. Cross-reference the Apache version:
   ```bash
   searchsploit apache 2.2.8
   ```
5. **Answer:** whichever the question asks for — the list of nikto findings, or the CVE for Apache 2.2.8.

**Gotcha:**
- Nikto is **noisy** — it flags theoretical issues. CEH usually wants the **concrete** findings (outdated version, exposed files) not generic header warnings.
- If the site is HTTPS, add `-ssl`. If it's on a weird port, `-h https://<IP>:8443`.

---

## Walkthrough 3: "Is 10.10.10.7 vulnerable to MS17-010? Provide evidence."

**Type:** targeted vuln check | **Difficulty:** easy | **Time:** 1 min

1. Confirm SMB is open:
   ```bash
   nmap -p 445 10.10.10.7
   ```
2. Run the exact NSE script:
   ```bash
   nmap -p 445 --script smb-vuln-ms17-010 10.10.10.7
   ```
3. Expected output:
   ```
   | smb-vuln-ms17-010:
   |   VULNERABLE:
   |   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
   |     State: VULNERABLE
   |     IDs:  CVE:CVE-2017-0144
   |     Risk factor: HIGH
   ```
4. **Answer:** `Yes, CVE-2017-0144` (or `MS17-010 / EternalBlue`, whichever the question asks for).

**Gotcha:**
- Some patched hosts still return `LIKELY VULNERABLE` because of fingerprinting quirks — verify with `auxiliary/scanner/smb/smb_ms17_010` in msfconsole.
- Exploitation step → [../05-system-hacking](../05-system-hacking/README.md) *(coming)*

---

## Walkthrough 4: "Identify the CVE in the SSH banner on 10.10.10.9"

**Type:** banner → CVE (OS service) | **Difficulty:** medium | **Time:** 3 min

1. Grab the banner:
   ```bash
   nmap -sV -p 22 10.10.10.9
   ```
   Output:
   ```
   22/tcp open  ssh  OpenSSH 7.2p1 Debian 5 (protocol 2.0)
   ```
2. searchsploit the product + version:
   ```bash
   searchsploit openssh 7.2
   ```
   Output:
   ```
   OpenSSH 2.3 < 7.7 - Username Enumeration | exploits/linux/remote/45233.py
   OpenSSH 7.2p1 - Xauth Command Injection   | exploits/linux/remote/39569.txt
   ```
3. Pick the matching version, open it, read the CVE header:
   ```bash
   searchsploit -x exploits/linux/remote/39569.txt
   ```
4. **Answer:** `CVE-2016-3115` (Xauth injection) — or the enum one (`CVE-2018-15473`) depending on wording.

**Gotcha:**
- The **protocol 2.0** string is NOT the version — don't copy it into searchsploit.
- If the banner is `SSH-2.0-OpenSSH_7.2p1` from netcat, strip the `SSH-2.0-` prefix before searching.
- Verify final CVE on https://nvd.nist.gov — CEH graders pull from NVD.
