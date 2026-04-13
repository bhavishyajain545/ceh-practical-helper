# Question Bank — Exam Strategy (6 drills)

> Not tool questions — scenario / decision-tree drills. Rehearse these the night before. You have 6 hours for 20 questions = ~18 min each, but some take 2 min and some eat 40.

**Legend:** 🟢 easy · 🟡 medium · 🔴 hard

---

### Q1 🟢 — First 5 commands on a fresh target IP (6 hours, nothing known)

**Category:** Opening moves | **Tools:** [nmap](../../tools/nmap.md), [exam-day playbook](../../exam-day/first-contact.md)

**Steps:**
1. Fast full-TCP sweep (kick off, then work on other Qs):
   ```bash
   nmap -p- -T4 --min-rate 2000 -oN all.nmap <IP> &
   ```
2. Top-1000 + version + default scripts (quick context):
   ```bash
   nmap -sV -sC -oN quick.nmap <IP>
   ```
3. UDP top-50 (SNMP/TFTP/DNS matter for CEH):
   ```bash
   sudo nmap -sU --top-ports 50 -oN udp.nmap <IP>
   ```
4. Web quick-look if 80/443 open:
   ```bash
   curl -sI http://<IP>/ ; whatweb http://<IP>/
   ```
5. Background dir brute if web present:
   ```bash
   gobuster dir -u http://<IP>/ -w /usr/share/wordlists/dirb/common.txt -o dirb.txt &
   ```

**Answer format:** N/A — this is the routine.

**Gotcha:** ALWAYS `-oN` every scan. You will need to grep output later and retyping a 2-minute scan is a waste you cannot afford.

---

### Q2 🟡 — Decision tree: the question asks for X, where do I look?

**Category:** Routing | **Tools:** [exam-day playbook](../../exam-day/decision-tree.md)

**Steps:**
1. "Version of service X" -> `nmap -sV -p <port>` -> 02-scanning.
2. "User / password / hash" -> john/hashcat + rockyou -> 06-system-hacking + 18-crypto.
3. "File inside image/PDF" -> steghide / binwalk / exiftool -> 18-crypto.
4. "Something on an S3 bucket" -> `aws s3 ls --no-sign-request` -> 17-cloud.
5. "Permission / package / activity in APK" -> apktool + jadx -> 15-mobile.
6. "Firmware / slave ID / PLC" -> nmap NSE `s7-info`/`modbus-discover` -> 16-iot-ot.
7. "SQL / XSS / LFI on a website" -> sqlmap / burp / ffuf -> 14-webapp.
8. "Wireless handshake / WEP key" -> aircrack-ng suite -> 13-wireless.

**Answer format:** pick one route and GO. Do not dabble.

**Gotcha:** if you're 5 minutes in and still routing, re-read the question — you've probably missed a keyword that picks the domain for you.

---

### Q3 🔴 — What to do when you're stuck on a question after 30 minutes

**Category:** Triage | **Tools:** brain, [exam-day/stuck.md](../../exam-day/stuck.md)

**Steps:**
1. SKIP. Note the Q number + a 1-line state (`Q7: cracked admin, no shell yet`) in a `notes.txt`.
2. Move to the next question you're confident on. Come back later.
3. When you return, re-read the question from scratch — often the answer format hint (integer? version? name?) tells you what tool you should've used.
4. If you have scans still running in the background (you should — see Q5), their output may now contain the answer.
5. Rule: never spend >45 min on one question on the first pass.

**Answer format:** N/A — triage habit.

**Gotcha:** CEH is graded on count, not order. A skipped-and-returned question scores the same as one solved on first contact. Ego costs points.

---

### Q4 🟢 — Answer format cheat sheet: what does CEH expect?

**Category:** Formatting | **Tools:** memory

**Steps:**
1. **Hashes:** just the hash, lowercase hex, no quotes, no `0x`.
2. **IPs:** dotted quad, no port, no CIDR unless asked (`10.10.10.5`).
3. **Versions:** as printed by the tool (`vsftpd 2.3.4`, `OpenSSH 7.2p2`) — don't "clean it up".
4. **Passwords / flags:** exact case, no trailing newline.
5. **Counts:** integer, no commas, no words (`42` not `forty-two` not `42 hosts`).
6. **CVE:** `CVE-2017-0144` format, uppercase.
7. **Domains / URLs:** lowercase, no trailing slash unless the question shows one.
8. **MAC:** colons, lowercase (`aa:bb:cc:dd:ee:ff`) unless shown otherwise.

**Answer format:** see above — the meta-question.

**Gotcha:** wrong format = wrong answer. `vsftpd2.3.4` and `vsftpd 2.3.4` are not the same string. Copy-paste from tool output whenever possible.

---

### Q5 🟡 — How to keep scans running in the background while solving other Qs

**Category:** Parallelism | **Tools:** `tmux`, bash `&`, `nohup`

**Steps:**
1. Start every session in tmux: `tmux new -s ceh`. Detach with `Ctrl-b d`, reattach `tmux a -t ceh`.
2. One tmux window per target IP. New window: `Ctrl-b c`. Switch: `Ctrl-b <n>`.
3. Long scans -> background: append `&`, then `jobs` / `fg %1` to check.
4. Or dedicate a pane: `Ctrl-b "` (horizontal split) — top pane runs scan, bottom pane you type in.
5. Always `-oN` the output so you can `grep` from any pane.
6. Typical background jobs: full `-p-` nmap, `gobuster`, `hashcat`, `stegcracker`.

**Answer format:** N/A — workflow.

**Gotcha:** do NOT run hashcat and a full port scan on the same VM at the same time — hashcat will pin CPU and make your scans time out. Stagger them.

---

### Q6 🔴 — End-of-exam checklist (last 30 minutes)

**Category:** Closing | **Tools:** checklist

**Steps:**
1. Re-read every answer you submitted — format check (Q4).
2. For every skipped Q, try one last ditch: re-read the question, grep all your `*.nmap` / `*.txt` files for keywords from the question.
3. Check background jobs: `jobs`, `tmux ls` — did anything finish with a useful result?
4. If hashcat is still running, `--show` against every wordlist result so far.
5. Submit EVERY question even if guessing — CEH has no negative marking.
6. Save all your notes/files off the VM if allowed (screenshots, `notes.txt`).

**Answer format:** N/A — ritual.

**Gotcha:** running out of time without submitting guesses on blanks is the single most common way candidates fail. Guess something on every blank before the clock hits zero.

---

## 📌 Quick links

- [exam-day first-contact playbook](../../exam-day/first-contact.md)
- [exam-day decision tree](../../exam-day/decision-tree.md)
- [exam-day stuck / triage](../../exam-day/stuck.md)
- [tool index](../../tools/)
- [all domain READMEs](../../README.md)
