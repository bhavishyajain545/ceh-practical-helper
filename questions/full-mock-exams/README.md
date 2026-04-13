# Full Mock Exams — CEH Practical

Three timed, full-length mock exams built to mimic the real CEH Practical exam: **20 questions in 6 hours**, spanning all 18 domains, mixing single-step drills with multi-step chains.

Use these in your **final week of prep**, after you've finished the per-domain question banks in [`../by-domain/`](../by-domain/).

---

## The three mocks

| Mock | Difficulty | When to sit | Focus |
|------|-----------|-------------|-------|
| [Mock 1](mock-1.md) | 🟢 Easy | Day -6 or -5 | Confidence, tool recall, baseline timing |
| [Mock 2](mock-2.md) | 🟡 Medium | Day -4 or -3 | Realistic balance, chain building |
| [Mock 3](mock-3.md) | 🔴 Hard | Day -2 | Stress test, longer chains, evasion |

Leave the day before the real exam for **light review only** — no more mocks.

---

## Rules for sitting a mock

1. **Clear your desk.** Notes closed. Per-domain files closed. Playbooks closed. The only thing allowed open is the actual tool reference pages under `/tools/` (the real exam gives you no internet, but the iLabs env has local man pages — treat tool docs as your man pages).
2. **Start a 6-hour timer.** No pauses except bathroom/food — the real exam does not pause.
3. **Scroll past the Solutions divider? Don't.** Each mock file has all 20 questions first, then a `# Solutions` section. Stop scrolling at the divider.
4. **Mark answers in a separate text file** — e.g. `mock-1-attempt.txt`. Write the question number, your command history, and your final answer in the exact format requested.
5. **Copy answers verbatim** from tool output. The real exam is strict on format: `vsftpd 2.3.4` is correct; `VSFTPD v2.3.4` may be marked wrong.
6. **Skip and return.** If a question eats >20 min, flag it and move on. Come back after Q20.
7. **Take notes on your mistakes** — this is what you actually review afterward.

---

## Scoring

CEH Practical passing is **70%** (14/20). Use these bands:

| Score | Verdict |
|-------|---------|
| 18–20 | Excellent — you will pass comfortably. Spot-check weak domains only. |
| 14–17 | Passing — identify the 3–6 you missed and re-drill those domains. |
| 10–13 | Borderline — re-drill 2 weakest domains, then try the next mock. |
| ≤ 9  | Not ready — go back to per-domain banks. Do not sit the next mock yet. |

**Timing bands** (independent of score):

| Finish time | Verdict |
|-------------|---------|
| < 4h | Too fast — you're probably skipping verification steps. Slow down. |
| 4h – 5h30 | Ideal pace. |
| 5h30 – 6h | Cutting it close — drill your slow domains. |
| > 6h | You would have run out of time. Identify which questions ate the clock. |

---

## After each mock — the 30-minute debrief

1. **Score yourself honestly.** Compare your answers to the Solutions block, not to your memory.
2. **Categorize every miss** into one of:
   - **Tool syntax** — you knew the approach but fumbled the flags → re-read the tool page.
   - **Wrong tool** — you used the wrong tool for the job → re-read the domain playbook.
   - **Missed a step** — you got 80% there but stopped early → practice the full chain.
   - **Format wrong** — your answer was right but formatted wrong → habit fix, always copy verbatim.
3. **Write 3 bullet lessons** at the bottom of your attempt file.
4. **Re-drill just the missed domains** — don't re-read everything.

---

## After all three mocks

- **Averaged ≥ 16/20 across Mocks 2 and 3** → you're ready. Spend the final day on light review of your cheat card and tool flags.
- **Averaged 12–15** → you'll likely pass but it'll be tight. Re-drill your bottom 3 domains.
- **Averaged < 12** → postpone the exam if possible. If you can't, focus hard on scanning + enum + SMB + web — those are the highest-density topics and cheapest wins.

---

## Realistic exam notes

- The real CEH Practical gives you ~4–6 target machines in an iLabs environment. Questions are grouped so you scan a box once and answer 3–4 questions about it — these mocks reflect that (see the "Targets in this mock" header).
- **You can answer in any order.** Do the easy scanning questions first to bank points, save chains for last.
- **Your answers are typed into a webform.** There is no partial credit. Right answer + wrong format = 0 points.
- **There is no penalty for guessing** — never leave an answer blank.

Good luck. Sit the first mock when ready.
