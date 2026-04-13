# ⚠️ Common Traps — Yahan Log Fail Hote Hain

> Real candidates ne yeh galtiyaan kari aur fail hue. Padhke avoid karo.

## Trap 1: Format galti

**Sabse common.** Already covered in [answer-format.md](answer-format.md). Fixing point: **verify before submit, every time**.

---

## Trap 2: 1 question pe 1 ghanta

**Symptoms:** Ek question pe stuck, ego mode on, "main solve karke hi hatunga". 60 min beet jate hain. 5 other questions miss ho jate hain.

**Fix:** Strict 30-min cap. SKIP karna strategy hai, weakness nahi.

---

## Trap 3: Tool ka galat output read karna

**Example:** nmap output mein 5 services hain, tum 1st wala read kar lete ho without checking question's port.

**Fix:**
- Question kis port ka bola? `-p <port>` se filter karo nmap mein
- Tool output mein Ctrl+F karke specific port dhundo

---

## Trap 4: Galat tool dictionary use karna

**Example:** Hash crack ke liye rockyou use kiya, password rockyou mein nahi tha. Confused.

**Fix:**
- rockyou pehla try (covers 80% common passwords)
- Phir specific lists (`seclists/Passwords/Common-Credentials/10k-most-common.txt`)
- Common admin passwords manually try karo (admin, password, 123456)

---

## Trap 5: `-Pn` na lagana

**Symptoms:** nmap "host seems down" bolta hai. Tum next tool try karte ho.

**Reality:** Host alive hai, ICMP block hai. Bas `-Pn` add karna tha.

**Fix:** Default mein `-Pn` lagao if you're sure target exists.

---

## Trap 6: sudo na lagana

**Symptoms:** `nmap -O` empty. `nmap -sS` not stealth. `nmap -sU` slow/empty.

**Fix:** Root scans need sudo. Ye flags need sudo:
- `-sS` (SYN stealth)
- `-sU` (UDP)
- `-O` (OS detection)
- `-A` (aggressive — includes -O)
- `--script` (some need root)

---

## Trap 7: Default-port assumptions

**Example:** SSH question, but SSH on port 2222, not 22. You scanned `-p 22` only and missed it.

**Fix:** Use `-p-` (all 65535) for thorough scan. Always.

---

## Trap 8: Forgetting to background

**Symptoms:** sqlmap dump 15 min le raha hai. You sit and watch. 15 min wasted.

**Fix:** ALWAYS background long commands with `&`. Move on. Check back later.

---

## Trap 9: Lost in tool output

**Symptoms:** nmap printed 200 lines. You scroll, get confused, miss the answer.

**Fix:**
- Save to file: `-oN file.txt`
- `grep` for keyword: `grep -i version file.txt`
- Or `less file.txt` + `/keyword`

---

## Trap 10: Re-typing target IP

**Symptoms:** Typed `10.10.10.5` 30 times in 6 hours. Once typed `10.10.0.5`. 10 min wasted debugging.

**Fix:** **Always copy-paste** the IP from the question. Never type.

Pro move: at exam start, copy each unique target IP into a "scratch" text file. Refer to it.

---

## Trap 11: Wordlist not unzipped

**Symptoms:** `hashcat ... rockyou.txt` → "file not found"

**Fix:** Parrot mein rockyou usually `.gz` form mein hota hai:
```bash
sudo gunzip /usr/share/wordlists/rockyou.txt.gz
ls -la /usr/share/wordlists/rockyou.txt   # should be ~140MB
```
Do this **before exam**, not in middle.

---

## Trap 12: Burp not configured

**Symptoms:** Burp Suite open kar diya, but Firefox proxy nahi set. Requests intercept nahi ho rahe.

**Fix:**
1. Open Burp
2. Firefox → Preferences → Network → Manual proxy → `127.0.0.1:8080`
3. Visit Burp → Proxy → Intercept ON
4. Refresh page in Firefox

In iLabs, Burp + Firefox usually pre-configured. Verify in prep.

---

## Trap 13: Galat metasploit module

**Symptoms:** `search smb` mein 50 results. You pick wrong one. Exploit fails.

**Fix:**
- Specific search: `search ms17-010`, `search vsftpd 2.3.4`
- Read description before use
- `info` after `use` to confirm
- `check` before `exploit` (some modules support it)

---

## Trap 14: Wireshark filter syntax confusion

**Display filter** vs **capture filter** — different syntax!

| Type | Syntax | Where |
|---|---|---|
| Display filter | `tcp.port == 21` | Top bar inside Wireshark |
| Capture filter | `tcp port 21` | When starting capture |

**Trap:** Using display syntax in capture filter (or vice versa).

**Fix:** For analysis use display filter. For live capture, mostly leave capture filter empty and filter via display filter after.

---

## Trap 15: Decompressed dump output too big

**Symptoms:** sqlmap dump shows 50,000 rows. You scroll forever.

**Fix:**
- Use `-T users` to dump only users table
- Use `-C username,password --dump` for specific columns
- Limit rows: `--start=1 --stop=10`

---

## Trap 16: Submitting answer for wrong question

**Symptoms:** Q5 ka answer Q3 ke box mein paste kiya. Submit. Both wrong.

**Fix:**
- Use a notepad: `Q1: ...`, `Q2: ...`
- Submit one at a time, verify Q number both times

---

## Trap 17: Trusting first scan output

**Symptoms:** Scan said port closed → you concluded service not there → moved on. Actually firewall was filtering, service was up.

**Fix:**
- Try `-Pn`
- Try `-sT` (TCP connect — different signature)
- Try different timing `-T2`

---

## Trap 18: Forgetting to start service

**Symptoms:** msf module set up, exploit run → "no session". Reverse shell port not open on attacker?

**Fix:**
- Check listener: `netstat -tlnp | grep <PORT>`
- Use `multi/handler` ALWAYS for custom payloads
- Verify LHOST = your iLabs interface IP, not 127.0.0.1

---

## Trap 19: Internet dependency

**Symptoms:** Your tool needs to download a wordlist / module / update. iLabs has restricted internet. Stuck.

**Fix:**
- Use what's already on Parrot (`/usr/share/wordlists`, `/usr/share/seclists`)
- Update msfdb / nmap scripts BEFORE exam
- Don't depend on online tools as primary (use as backup)

---

## Trap 20: Skipping verification at the end

**Symptoms:** Last 30 min you keep solving more. Don't verify. Submit. 4 format errors slip through.

**Fix:** **Last 30 min = verify only.** No new questions. Just check formats.

---

## Trap 21: Panic loop

**Symptoms:** 1 question stumped you. Anxiety builds. Next question — you can't focus. Brain freezes. Cascading failure.

**Fix:**
- Stop. Stand up. 30 sec walk if allowed.
- Drink water.
- Pick a 🟢 easy question to break the loop. Win one. Confidence back.
- Then return to medium ones.

---

## Trap 22: Underestimating crypto/stego

**Symptoms:** "Stego/crypto easy hai" — assumption. Question gives weird image. You spent 1 hour on it.

**Reality:** Crypto/stego often hide in non-obvious places. Need patience. Aperi'Solve, exiftool, binwalk, steghide — ALL of them, in order.

**Fix:** Stego workflow already in [18-cryptography/walkthroughs.md](../18-cryptography/walkthroughs.md). Follow it sequentially.

---

## Trap 23: Wireless: WPA without handshake

**Symptoms:** Captured pcap, `aircrack-ng cap.cap` says "no valid WPA handshakes".

**Fix:**
- Need 4-way handshake. Without it, no crack possible.
- Force handshake by deauth: `aireplay-ng --deauth 5 -a <BSSID> wlan0mon`
- Re-capture
- Verify with `aircrack-ng cap.cap` first

---

## Trap 24: Reading the question on autopilot

**Symptoms:** You're tired (4 hours in). Read Q11 fast. Spend 20 min on wrong interpretation. Re-read — completely different question.

**Fix:**
- Take a 1-min break every hour
- For each question, **read out loud quietly** (forces attention)
- If 4+ hours in, slow down questions, not speed up

---

## Trap 25: Not using the repo

**Symptoms:** You memorized commands. Don't open repo. Forget a flag. Waste time recalling.

**Fix:** **Ye repo tumne kyun banaya hai? USE IT.** Open in Firefox at exam start. Refer to it constantly. Memorization is bad strategy when reference is allowed.

---

## The summary trap

> "I prepared, I'll be fine"

Wrong. Preparation gives you the **chance** to do well. Strategy + format + time mgmt + repo usage is what makes preparation **convert** into marks.

Read these traps once. Then again 1 day before exam. Internalize them.
