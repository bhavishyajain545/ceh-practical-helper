# ✅ Exam Day Checklist

> Exam se 1 din pehle aur exam ki subah ye list follow karo. Step by step.

## 1 day before

### Tech check
- [ ] iLabs login working — login karke verify karo
- [ ] Repo URL working in Firefox — actual page load karke check
- [ ] System requirements check — RAM, browser, internet
- [ ] Backup browser ready (Chrome) in case Firefox crashes
- [ ] Water bottle, snacks, coffee/chai ready
- [ ] Notepad + pen

### Mental check
- [ ] [strategies/mindset.md](mindset.md) read karo
- [ ] [strategies/time-strategy.md](time-strategy.md) read karo
- [ ] [strategies/common-traps.md](common-traps.md) read karo
- [ ] [strategies/answer-format.md](answer-format.md) read karo

### Sleep
- [ ] **Sleep 7-8 hours.** Pulling all-nighter = guaranteed fail.
- [ ] Phone bedroom se duur. Charger lo.
- [ ] Alarm set. Backup alarm bhi.

### NO last-minute new learning
- Mock exam mat lo
- New tool mat seekho
- Mind already full hai. Rest > more knowledge.

---

## Morning of exam

### 2 hours before
- [ ] Wake up
- [ ] Light breakfast (no heavy food — sleepy ho jaoge)
- [ ] Bathroom routine
- [ ] Shower
- [ ] Comfortable clothes (no tight stuff)

### 1 hour before
- [ ] Coffee/chai (1 cup, not 5)
- [ ] Open Firefox + repo URL — verify it loads
- [ ] Open backup tabs:
  - [ ] CyberChef: https://gchq.github.io/CyberChef/
  - [ ] CrackStation: https://crackstation.net/
  - [ ] Exploit-DB: https://www.exploit-db.com/
  - [ ] CVE Details: https://www.cvedetails.com/
- [ ] Notepad open (text file ya physical)
- [ ] Phone: silent, AWAY from desk
- [ ] Bathroom break

### 30 min before
- [ ] Login to iLabs
- [ ] Read exam instructions
- [ ] Verify exam version (v12 or v13)
- [ ] Verify time, scoring, format
- [ ] Verify Parrot VM is launching properly

### 5 min before
- [ ] Deep breath x3
- [ ] [mindset.md](mindset.md) ki last 2 lines yaad karo
- [ ] **"30 min max per Q. Skip allowed. 14/20 wins."**

---

## During exam — first 30 minutes (Phase 1)

- [ ] **Don't solve any questions yet**
- [ ] Read all 20 questions, one by one
- [ ] For each Q, write in notepad:
  ```
  Q1: target=10.10.10.5, ask=FTP version, type=scanning, diff=easy, format=version
  Q2: target=10.10.10.5, ask=SMB users, type=enum, diff=medium, format=user list
  ...
  ```
- [ ] Identify unique targets (e.g. .5, .20, .40, .50)
- [ ] **Background: kick off mega scans for each target** in 4 terminal tabs:
  ```bash
  nmap -sV -sC -p- -T4 -oA t5 10.10.10.5 &
  nmap -sV -sC -p- -T4 -oA t20 10.10.10.20 &
  ...
  ```

---

## During exam — Phase 2 (90 min: easy kills)

- [ ] Sort questions by difficulty (notepad mein 🟢 ones list karo)
- [ ] Solve 🟢 ones in batches by target
- [ ] Each Q: 5-10 min max
- [ ] Format verify before submitting
- [ ] Update notepad: `Q1 ✅ done`, `Q3 ✅ done`

**End of Phase 2: 6-8 questions done. Time: 2:00.**

---

## During exam — Phase 3 (3 hours: medium crush)

- [ ] Solve 🟡 ones
- [ ] 15-20 min each
- [ ] Background long commands always
- [ ] If stuck 25+ min → SKIP, mark
- [ ] Take 1-min mental break every hour

**End of Phase 3: 14-16 questions done. Time: 5:00.**

---

## During exam — Phase 4 (60 min: hard + verify)

### First 30 min
- [ ] Hard 🔴 questions
- [ ] Skipped questions revisit (with new ideas)
- [ ] 25-min cap each

### Last 30 min — VERIFY ONLY
- [ ] Read each submitted answer
- [ ] Check format against question
- [ ] Trim whitespace
- [ ] Case correctness
- [ ] No new questions started in last 30 min

---

## What to do if disaster strikes

### Internet down for 5+ minutes
- Stay calm. Don't refresh repeatedly.
- Local Parrot tools still work
- Wait. Notify proctor.

### iLabs VM crashes
- Notify proctor immediately
- They can reset
- Time usually paused

### Browser crashes
- Open backup browser
- Bookmark bar should reload
- Repo on GitHub still accessible

### You realize you misread a question
- Don't panic
- 30 sec to read again carefully
- Adjust answer
- Move on

### Time pressure (1 hour left, 8 questions left)
- ABANDON hope of solving all 8
- Pick the 4 easiest from the 8
- Solve those well
- Best-guess the rest
- 14/20 still possible

---

## Submission etiquette

- Final submit only when sure
- Don't submit at first instinct on hard ones
- But also: don't keep flipping answers — first instinct on easy ones is usually right

---

## After submission

- [ ] Wait for results
- [ ] Don't discuss specific questions with anyone (NDA)
- [ ] Save notepad for personal review only

---

## Failure scenario (just in case)

Agar fail hue:
- It's okay. Many CEH practical takers fail first attempt.
- Voucher allows retake (sometimes free, sometimes paid)
- Identify weak domains, study those, retry in 4-6 weeks
- This repo is yours — refine it based on what you saw
- **Failing once doesn't define you.** Trying again does.

---

## Success scenario

Agar pass hue:
- Celebrate
- Add CEH Practical to LinkedIn / resume
- Help someone else prepare (this repo can help them too)
- Move to next cert: OSCP, CRTP, CRTE, etc.

---

## The 3 most important things on exam day

1. **Read each question 2x.** Format mostly costs marks.
2. **Background everything.** Parallel work = +2 hours saved.
3. **30-min skip rule.** Don't get attached to a stuck Q.

If you do these 3, you'll pass.
