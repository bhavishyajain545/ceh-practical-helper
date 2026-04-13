# ⏱ Time Strategy — 6 Ghante, 20 Sawal

> 6 hours / 20 questions = **18 min average per question**. Lekin yeh average hai. Real plan alag hai.

## The 4-phase plan

```
┌──────────────────────────────────────────────┐
│ Phase 1: SCAN (30 min)                       │
│ - All 20 questions ek-ek karke padho          │
│ - Targets note karo                            │
│ - Difficulty estimate karo (🟢🟡🔴)            │
│ - Group by target                              │
├──────────────────────────────────────────────┤
│ Phase 2: EASY KILL (90 min)                   │
│ - 🟢 questions solve karo (6-8 expected)       │
│ - 5-10 min each = ~60-80 min                   │
│ - End of Phase 2 = ~30-40% done                │
├──────────────────────────────────────────────┤
│ Phase 3: MEDIUM CRUSH (180 min)               │
│ - 🟡 questions (8-10 expected)                 │
│ - 15-20 min each                                │
│ - End of Phase 3 = ~85% done                   │
├──────────────────────────────────────────────┤
│ Phase 4: HARD + VERIFY (60 min)               │
│ - Hard 🔴 (2-4 questions, 15-25 min each)      │
│ - Last 30 min: VERIFY all answers              │
│ - Format check, re-read questions              │
└──────────────────────────────────────────────┘
```

---

## Phase 1 in detail (most important)

**Pehla 30 min koi solve nahi karna.** Sirf reconnaissance. Ye sabse counter-intuitive baat hai.

### Kya karna hai:
1. Question 1 open karo, padho, **solve mat karo**
2. Notepad mein likho:
   - Q number
   - Target IP(s)
   - Type (scanning / web / crypto / etc.)
   - Difficulty guess (🟢/🟡/🔴)
   - Suspected tool
3. Repeat for all 20

### Kyun?
- **Same target = shared scans.** Q3, Q7, Q11 sab 10.10.10.5 pe hain? Ek nmap scan se 3 questions cover ho jayenge.
- **Difficulty distribution dikhega.** Maybe 12 easy hain — relax karo.
- **Mental model build hota hai** — exam ke baare mein "feel" aata hai.
- **Background scans start hote hain.** Phase 1 ke beech mein hi all targets pe nmap background mein chal sakta hai.

### Tactical move
Phase 1 ke beech mein hi ye terminal commands chala do (har unique target ke liye):
```bash
# Tab 1
nmap -sV -sC -p- -T4 -oA t1 10.10.10.5 &

# Tab 2
nmap -sV -sC -p- -T4 -oA t2 10.10.10.20 &

# Tab 3
nmap -sV -sC -p- -T4 -oA t3 10.10.10.40 &
```
Phase 2 start hote hi `cat t1.nmap` etc se output ready mil jayega.

---

## Time per phase — strict budgets

| Phase | Time | Cumulative | Goal |
|---|---|---|---|
| 1: Scan | 30 min | 0:30 | All Qs read, scans started |
| 2: Easy | 90 min | 2:00 | 6-8 Qs done |
| 3: Medium | 180 min | 5:00 | 14-16 Qs done |
| 4: Hard + Verify | 60 min | 6:00 | Final answers locked |

**Discipline:** Phase ka time over hone par next phase mein move karo, even if behind. Stuck Q ko mark karo.

---

## Per-question time budget

| Difficulty | Time on first attempt |
|---|---|
| 🟢 Easy | **5-8 min** (max 10) |
| 🟡 Medium | **15-20 min** (max 25) |
| 🔴 Hard | **20-30 min** (max 30) |

**Hard rule:** **30 min se zyada koi question first pass mein nahi.** Mark karo, skip karo.

---

## When to skip

Skip karo agar:
- 25 min ho gaye aur progress nahi
- Tool crash ho raha hai aur fix nahi mil raha
- Output samajh nahi aa raha aur repo mein answer nahi
- Frustration build ho raha hai

Skip karne ka tareeqa:
1. Notepad mein likho: `Q7: SKIPPED at 25 min — stuck on hash extraction`
2. Best guess answer likh do (kabhi kabhi guess sahi nikalta hai)
3. Move to next question
4. Phase 4 mein wapas aana

---

## Background everything

Yeh tactic single sabse zyada time bachata hai. **Hamesha do cheez ek saath chalao:**

```bash
# Heavy command background mein
sqlmap -u "http://10.10.10.5/x?id=1" --batch --dump &

# Tum doosra question solve karo
nmap -sV 10.10.10.20

# Ya hash crack chalu raho background mein
hashcat -m 1000 hashes.txt rockyou.txt &
```

Use `jobs` to see what's running, `fg` to bring back, `tail -f file` to peek.

→ Detail: [parallel-work.md](parallel-work.md)

---

## The 30-minute checkpoint

**Har 30 min mein 1 min stop karke ye check karo:**
- Kitne questions done? (target progress curve dekhne ke liye)
- Kya stuck hu? Skip karna chahiye?
- Background scans complete hue? Output check karo
- Pani / break needed?

Set a quiet timer mentally (har 30 min). Ye habit hai jo prep mein build karni hai.

---

## Last 30 min strategy

Last half-hour completely **verification mode**:
1. Saare submitted answers ek list mein dekh lo
2. Har answer ke liye:
   - Question dubara padho
   - Format match karta hai? (e.g. version verbatim, integer, hostname dot-notation)
   - Trailing whitespace nahi hai?
   - Case sensitivity sahi hai?
3. Marked/skipped questions wapas try karo (agar time ho)
4. **Confidence se submit karo** — second-guessing mein answer flip mat karna unless sure

---

## Common time leaks

| Leak | Time wasted | Fix |
|---|---|---|
| 1 question pe 1 ghanta | 60 min | Strict 30-min cap |
| Tool re-installing | 10-30 min | Pre-test in prep — Parrot has all |
| Reading repo aimlessly | 10 min/question | Use Ctrl+F + master playbook |
| Re-running same scan | 5-15 min | Use `-oA` always, never re-scan |
| Typing target IP wrong | 5 min | Always copy-paste, never type |
| Answer format galti | 5 min/wrong | Verify format BEFORE submitting |

---

## Mental anchor

> **"30 minutes max. Skip is allowed. 14/20 is winning."**

Yeh teen lines yaad rakhna. Bas.
