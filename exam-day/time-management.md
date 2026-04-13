# Time management — 20 questions in 6 hours

**= 18 minutes per question average.** But not all questions take equal time.

## Strategy

1. **First 30 min — recon pass.** Open ALL 20 questions, read them, note target IPs and what each asks for. Don't solve yet.
2. **Group questions by target.** Questions on the same machine share scans. Solve those together.
3. **Easy first.** Knock out the 🟢 questions (version/port/OS detection) in 5 min each = 30–40 min for 6–8 questions = 30%+ already done.
4. **Medium next.** Enumeration, basic exploitation, web — 15–20 min each.
5. **Hard last.** Hash cracking, sqlmap full dump, wireless cracking — these can eat an hour. Don't let them eat two.
6. **Last 30 min — sanity check.** Re-verify answer formats. EC-Council is picky about formatting.

## Background everything

Always run scans/cracks in `&` background and move on:

```bash
nmap -sV -sC -p- -T4 -oA full <IP> &
hashcat -m 1000 hashes.txt rockyou.txt &
sqlmap -u "http://<IP>/x?id=1" --batch --dump &
```

Then `jobs` / `fg` / `tail -f` when you want to check.

## Hard rules

- **No question gets more than 30 min on first pass.** Mark and skip.
- **Save outputs to disk** (`-oA`, `tee`, `> file`) — exam UI may glitch.
- **Never type a target IP from memory** — copy-paste from the question.
- **Verify answer format before submitting.** Lowercase? Hyphenated? Trailing dot?
