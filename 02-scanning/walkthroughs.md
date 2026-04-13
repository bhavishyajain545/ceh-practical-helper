# 02 Scanning — full walkthroughs

End-to-end examples. These mirror how a real exam question flows.

---

## Walkthrough 1: "Find the version of the FTP server on 10.10.10.5"

**Type:** version detection | **Difficulty:** easy | **Time:** 1–2 min

1. Quick port check first (is FTP even up?):
   ```bash
   nmap -p 21 10.10.10.5
   ```
2. If `21/tcp open ftp`, run version detection ([why -sV](../tools/nmap.md#scan-techniques)):
   ```bash
   nmap -sV -p 21 10.10.10.5
   ```
3. Read the VERSION column. Example output:
   ```
   PORT   STATE SERVICE VERSION
   21/tcp open  ftp     vsftpd 2.3.4
   ```
4. **Answer:** `vsftpd 2.3.4` (copy verbatim — formatting matters)

**Gotcha:** if it shows `filtered`, add `-Pn`:
```bash
nmap -sV -Pn -p 21 10.10.10.5
```

---

## Walkthrough 2: "How many live hosts in 10.10.10.0/24?"

1. Ping sweep:
   ```bash
   nmap -sn 10.10.10.0/24
   ```
2. Count alive hosts in one shot:
   ```bash
   nmap -sn 10.10.10.0/24 | grep -c "Host is up"
   ```
3. **Answer:** the integer that grep prints.

**Gotcha:** if hosts block ICMP, use `-PR` (ARP) on the LAN, or `-PS80,443`:
```bash
sudo nmap -PR 10.10.10.0/24
```

---

## Walkthrough 3: "Find a vulnerable SMB service on 10.10.10.7"

1. Confirm SMB is open:
   ```bash
   nmap -p 445 10.10.10.7
   ```
2. Run all SMB vuln scripts:
   ```bash
   nmap -p 445 --script "smb-vuln-*" 10.10.10.7
   ```
3. Look for `VULNERABLE:` lines. Common find:
   ```
   | smb-vuln-ms17-010:
   |   VULNERABLE: Remote Code Execution vulnerability ... (EternalBlue)
   ```
4. **Answer:** `MS17-010` (or `EternalBlue`, depending on what they ask for)

→ Next step is usually exploitation: see [05-system-hacking](../05-system-hacking/README.md)

---

## Walkthrough 4: "Find all open ports + services on 10.10.10.20"

This is the bread-and-butter exam question.

1. Kick off the full scan in background while you read the next question:
   ```bash
   nmap -sV -sC -p- -T4 -oA full 10.10.10.20
   ```
2. While that runs (~2-5 min), do a fast scan to give immediate answers:
   ```bash
   nmap -sV --top-ports 1000 10.10.10.20
   ```
3. When the full scan finishes, read `full.nmap`.
4. **Answer:** count of `open` ports, or list with versions — depends on question wording.

**Tip:** ALWAYS use `-oA` so you can refer back. You will lose data otherwise.
