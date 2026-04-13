# 📅 3-Week Study Plan

> Day-by-day plan. Skip days hote rahenge — flexible rakhna, but try to hit all 21 days.

## Week-by-week overview

| Week | Goal | Output |
|---|---|---|
| **Week 1** | Familiarity | Read repo, drill basics, hands-on top 10 tools |
| **Week 2** | Depth | Drill all per-domain Qs, work scenarios |
| **Week 3** | Speed | Mock exams, refine, fix weak areas |

---

## Week 1 — Familiarity (Days 1-7)

**Goal:** Repo navigate karna seekho, top tools hands-on use karo, mental model build karo.

### Day 1 (Sunday) — Repo Tour
- [ ] Repo open karo Firefox mein
- [ ] [README.md](../README.md) padho
- [ ] [00-exam-strategy](../00-exam-strategy/README.md) padho
- [ ] [exam-day/first-5-commands.md](../exam-day/first-5-commands.md) padho
- [ ] [strategies/mindset.md](mindset.md) padho
- [ ] [strategies/time-strategy.md](time-strategy.md) padho
- **Time:** 2 hours

### Day 2 (Monday) — Scanning Mastery
- [ ] [02-scanning/README.md](../02-scanning/README.md) + commands + walkthroughs
- [ ] [tools/nmap.md](../tools/nmap.md) full read
- [ ] Hands-on: nmap on a TryHackMe / HTB box. Practice all flags.
- [ ] Solve 6 questions from [questions/by-domain/02-scanning.md](../questions/by-domain/02-scanning.md)
- **Time:** 3 hours

### Day 3 (Tuesday) — Enumeration
- [ ] [03-enumeration/README.md](../03-enumeration/README.md) full
- [ ] [tools/enum4linux.md](../tools/enum4linux.md), [smbclient.md](../tools/smbclient.md), [snmpwalk.md](../tools/snmpwalk.md)
- [ ] Hands-on: enum4linux against a vulnerable SMB box
- [ ] Solve 6 questions from [questions/by-domain/03-enumeration.md](../questions/by-domain/03-enumeration.md)
- **Time:** 2.5 hours

### Day 4 (Wednesday) — System Hacking
- [ ] [05-system-hacking/README.md](../05-system-hacking/README.md) full
- [ ] [tools/metasploit.md](../tools/metasploit.md), [hydra.md](../tools/hydra.md), [john.md](../tools/john.md), [hashcat.md](../tools/hashcat.md)
- [ ] Hands-on: msfconsole on Metasploitable VM. Run an MS17-010 exploit.
- [ ] Solve 6 questions from [questions/by-domain/05-system-hacking.md](../questions/by-domain/05-system-hacking.md)
- **Time:** 4 hours (longest day)

### Day 5 (Thursday) — Web Apps + SQLi
- [ ] [12-web-apps/README.md](../12-web-apps/README.md) + [13-sqli/README.md](../13-sqli/README.md)
- [ ] [tools/sqlmap.md](../tools/sqlmap.md), [gobuster.md](../tools/gobuster.md), [wpscan.md](../tools/wpscan.md)
- [ ] Hands-on: sqlmap on DVWA. Step through all stages.
- [ ] Solve 6 questions each from web-apps and sqli banks
- **Time:** 3 hours

### Day 6 (Friday) — Cryptography + Stego
- [ ] [18-cryptography/README.md](../18-cryptography/README.md) full
- [ ] [tools/openssl.md](../tools/openssl.md), [steghide.md](../tools/steghide.md), [exiftool.md](../tools/exiftool.md), [binwalk.md](../tools/binwalk.md)
- [ ] [online-tools/crypto-tools.md](../online-tools/crypto-tools.md) (CyberChef!)
- [ ] [online-tools/stego-tools.md](../online-tools/stego-tools.md) (Aperi'Solve!)
- [ ] Hands-on: extract data from sample stego images
- [ ] Solve 6 questions from [questions/by-domain/18-cryptography.md](../questions/by-domain/18-cryptography.md)
- **Time:** 3 hours

### Day 7 (Saturday) — Catch up + Strategy
- [ ] Catch up on missed days
- [ ] Read all [strategies/](README.md) files
- [ ] [strategies/parallel-work.md](parallel-work.md) — practice tmux/terminator setup
- [ ] **Self-assessment:** Which domains feel weakest?
- **Time:** 3 hours

**End of Week 1 checkpoint:**
- Repo navigation natural ho gayi?
- Top 8 tools hands-on use kiya?
- Strategies internalize ho gayi?

---

## Week 2 — Depth (Days 8-14)

**Goal:** Saare domains drill, scenarios start, multi-step thinking build.

### Day 8 (Sunday) — Sniffing + Wireless
- [ ] [07-sniffing/README.md](../07-sniffing/README.md) + [tools/wireshark.md](../tools/wireshark.md)
- [ ] [14-wireless/README.md](../14-wireless/README.md) + [tools/aircrack-ng.md](../tools/aircrack-ng.md)
- [ ] Hands-on: open a sample pcap, practice display filters
- [ ] Solve 6 from each bank
- **Time:** 3 hours

### Day 9 (Monday) — Recon + Footprinting + Vuln Analysis
- [ ] [01-recon-footprinting/README.md](../01-recon-footprinting/README.md)
- [ ] [04-vuln-analysis/README.md](../04-vuln-analysis/README.md)
- [ ] [tools/dig.md](../tools/dig.md), [theHarvester.md](../tools/theharvester.md), [nikto.md](../tools/nikto.md)
- [ ] [online-tools/recon-tools.md](../online-tools/recon-tools.md) — bookmark Shodan, crt.sh
- [ ] Solve 6 from each bank
- **Time:** 2.5 hours

### Day 10 (Tuesday) — Web Servers + Sniffing extras
- [ ] [11-web-servers/README.md](../11-web-servers/README.md)
- [ ] [tools/curl.md](../tools/curl.md), [whatweb.md](../tools/whatweb.md), [wafw00f.md](../tools/wafw00f.md)
- [ ] Hands-on: curl-based recon against sample sites
- [ ] Solve 6 from web-servers bank
- **Time:** 2 hours

### Day 11 (Wednesday) — Mobile + Cloud + IoT
- [ ] [15-mobile/README.md](../15-mobile/README.md) + [16-iot-ot/README.md](../16-iot-ot/README.md) + [17-cloud/README.md](../17-cloud/README.md)
- [ ] [tools/apktool.md](../tools/apktool.md), [jadx.md](../tools/jadx.md), [aws-cli.md](../tools/aws-cli.md)
- [ ] Solve 6 from each bank (or 4 each if time)
- **Time:** 2.5 hours

### Day 12 (Thursday) — Malware + DoS + Social Eng
- [ ] [06-malware-analysis/README.md](../06-malware-analysis/README.md)
- [ ] [09-dos/README.md](../09-dos/README.md)
- [ ] [08-social-engineering/README.md](../08-social-engineering/README.md)
- [ ] [10-session-hijacking/README.md](../10-session-hijacking/README.md)
- [ ] Solve 4 from each
- **Time:** 2 hours

### Day 13 (Friday) — Scenarios Set 1 (1-15)
- [ ] [questions/scenarios/README.md](../questions/scenarios/README.md)
- [ ] Work through scenarios 1-7 (45 min each, walk through commands)
- **Time:** 4 hours

### Day 14 (Saturday) — Scenarios Set 1 (8-15)
- [ ] Scenarios 8-15
- **Time:** 4 hours

**End of Week 2 checkpoint:**
- All 18 domain question banks completed at least once?
- Half the scenarios worked through?
- Can you recall which tool to use for any question type without thinking?

---

## Week 3 — Speed (Days 15-21)

**Goal:** Mock exams, refine weak areas, lock down format discipline.

### Day 15 (Sunday) — Scenarios Set 2 (16-30)
- [ ] Scenarios 16-23
- **Time:** 4 hours

### Day 16 (Monday) — Scenarios Set 2 continued
- [ ] Scenarios 24-30
- [ ] Re-read [strategies/answer-format.md](answer-format.md)
- **Time:** 4 hours

### Day 17 (Tuesday) — Mock Exam 1 (Easy)
- [ ] **Take [mock-1.md](../questions/full-mock-exams/mock-1.md) under timed conditions (6 hours)**
- [ ] No looking at solutions
- [ ] Mark answers in a notepad
- **Time:** 6 hours
- **Evening:** Check answers. Note what you got wrong and **WHY**.

### Day 18 (Wednesday) — Mock 1 Debrief + Weak Area Focus
- [ ] Identify weak domains from Mock 1
- [ ] Re-read those domain READMEs + tool docs
- [ ] Re-do failed questions
- **Time:** 3 hours

### Day 19 (Thursday) — Mock Exam 2 (Medium)
- [ ] **Take [mock-2.md](../questions/full-mock-exams/mock-2.md) under timed conditions**
- **Time:** 6 hours
- **Evening:** Debrief

### Day 20 (Friday) — Final Refinement
- [ ] Re-read [strategies/common-traps.md](common-traps.md)
- [ ] Re-read [strategies/exam-day-checklist.md](exam-day-checklist.md)
- [ ] Practice tmux + parallel commands
- [ ] Bookmark critical online tools
- [ ] Verify all tools work in your prep environment
- **Time:** 2 hours
- **Rest day:** sleep early

### Day 21 (Saturday) — Mock Exam 3 (Hard) OR Rest
- **Option A:** Take [mock-3.md](../questions/full-mock-exams/mock-3.md) (6 hours)
- **Option B:** REST. Don't burn out the day before.

**Recommend Option B if Mock 1 + 2 went well.**

### Day 22 (Sunday) — EXAM DAY
- See [exam-day-checklist.md](exam-day-checklist.md)

---

## Daily routine within each day

1. **Warm-up (10 min):** Read 1 strategies/ file
2. **Theory (30-60 min):** Read domain README + tool doc
3. **Practice (60-120 min):** Hands-on with the tool
4. **Drill (30-60 min):** Solve question bank Qs
5. **Review (10 min):** What did I learn? Note in journal.

---

## Adjusting if behind

If you've fallen behind by Day 14:
- **Skip:** Mobile, IoT, Cloud (lowest ROI — fewer questions usually)
- **Skip:** Social Engineering, DoS (procedural, less hands-on needed)
- **Don't skip:** Scanning, Enumeration, System Hacking, SQLi, Crypto

If you've fallen behind by Day 17:
- Take only Mock 1 and Mock 3
- Skip detailed debrief, just identify top 3 weak areas
- Spend 2 days fixing those

---

## Adjusting if ahead

If you're ahead by Day 14:
- Take Mock 1 early
- Spend more time on hardest scenarios (privesc, AD, RSA)
- Practice non-obvious areas: PowerShell, evil-winrm, AS-REP roast

---

## Energy management

- **Sleep 7+ hours.** Yeh non-negotiable hai. Brain needs sleep to consolidate.
- **Hydrate.** Coffee/chai theek hai but sirf water bhi pio.
- **Walk.** 20 min/day minimum. Helps memory.
- **Phone ban during prep.** Distractions kill deep work.
- **Don't binge late nights.** 4-hour quality > 8-hour distracted.

---

## Mental health

- Anxious feelings normal hain
- Compare yourself to **yesterday's you**, not other people
- Talking to family/friends helps
- If overwhelmed: read [mindset.md](mindset.md) again
- Remember: 14/20 = pass. **You don't need 20/20.**
