# 00 — Exam Strategy

> Meta-domain. Read this **first**, then again the night before. The exam is 6 hours, 20 questions, open book, open internet. You lose points for wasted time, not for using tools.

## 🧭 Decision tree — "I just opened a new question"

```
New question appears
│
├── Read it TWICE. Underline the exact noun being asked for.
│   (version string? CVE? file contents? flag? user? hash?)
│
├── What domain? (scanning / enum / web / crypto / ...)
│   └── Jump to that folder's README decision tree
│
├── Do I have an IP / URL / host?
│   ├── yes → run the 5 first-moves (see commands.md)
│   └── no  → it's theory / tool-identification → search tool docs
│
├── Is this a "find X on the machine" question?
│   └── need a foothold first → scanning → enum → exploit
│
└── Stuck > 15 min?
    └── FLAG IT, move on, come back. Never burn 40 min on one Q.
```

## 📄 Files in this folder

- **[commands.md](commands.md)** — the 5 first-moves recipe
- **[walkthroughs.md](walkthroughs.md)** — anatomy of a CEH question
- Exam-day cheat: **[../exam-day/first-5-commands.md](../exam-day/first-5-commands.md)**

## 🛠 What's allowed

- Full internet access (Google, exploit-db, GitHub, vendor docs)
- Any tool on Parrot / Kali (the exam VM ships with both)
- Your own notes / this repo in a browser tab
- **Not allowed:** asking other humans, LLMs that act agentically on the target

## ✅ Exam-day checklist

- [ ] This repo open in Firefox tab 1
- [ ] Terminal with `tmux` — 4 panes: scan / enum / notes / scratch
- [ ] `~/exam/` directory with one sub-folder per question
- [ ] Always use `nmap -oA` so scans are saved
- [ ] Read every question TWICE before typing anything
- [ ] Copy answers **verbatim** — `vsftpd 2.3.4` not `vsftpd-2.3.4`
- [ ] Flag & skip anything stuck > 15 min
- [ ] Final 30 min: revisit flagged questions

## Common pitfalls

- Typing the version wrong (dashes, casing, trailing spaces)
- Forgetting `-Pn` when host blocks ICMP
- Forgetting `sudo` for `-sS`, `-sU`, `-O`
- Running `-p-` when the question only needs top-1000 (wastes minutes)
- Not saving scan output — then having to re-scan
- Skipping UDP entirely (SNMP / DNS / TFTP live there)
