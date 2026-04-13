# ⚡ Parallel Work — Ek Time Pe 3 Cheezein

> Sequential mat chala. Parallel chala. Yeh single trick 1+ ghanta bachata hai 6-hour exam mein.

## The core idea

CEH practical mein **wait time** sabse bada time-killer hai:
- nmap full scan: 5-10 min wait
- sqlmap dump: 5-15 min wait
- hashcat crack: variable
- gobuster dir enum: 3-5 min wait

Sequentially: 4 commands × 8 min = **32 min sirf wait karne mein**.
Parallel: all 4 chalu, 8 min total. **24 min bachaye.**

---

## Setup: Multiple terminals

iLabs Parrot mein **terminator** ya **tmux** ya simply multiple terminal windows use karo.

### Option 1: Multiple terminal tabs/windows
- Open 4-5 terminals before exam start
- Label mentally: "Tab 1 = Q3 scan", "Tab 2 = Q5 sqlmap", etc.
- Most reliable

### Option 2: tmux (advanced, faster)
```bash
tmux new -s exam
# Inside tmux:
# Ctrl+B then % → vertical split
# Ctrl+B then " → horizontal split
# Ctrl+B then arrow → switch panes
# Ctrl+B then d → detach (later: tmux a)
```

### Option 3: terminator (graphical, easy)
- Right-click → Split Horizontally / Vertically
- Multiple panes in one window

**Pick one and PRACTICE it in prep.** Exam mein switching mental load nahi chahiye.

---

## Background commands with `&`

Sabse simple parallel trick:

```bash
nmap -sV -sC -p- -T4 -oA full 10.10.10.5 &
```

Ye command background mein chalegi, terminal turant free ho jayega. Output `full.nmap` mein save ho raha hoga.

### Useful background commands
| Command | Why background |
|---|---|
| `nmap ... &` | Long scan |
| `sqlmap ... --batch &` | Long dump |
| `hashcat ... &` | GPU work |
| `gobuster ... &` | Dir enum |
| `ffuf ... &` | Fuzz |
| `nikto ... &` | Web vuln scan |

### Check background jobs
```bash
jobs                # list running background jobs
fg %1               # bring job 1 to foreground
kill %1             # kill job 1
```

### Watch progress without blocking
```bash
tail -f full.nmap   # see nmap output as it grows
tail -f sqlmap.log
```

---

## The "kick off everything" pattern

When you first see a target, kick off ALL likely scans in background **before** you even read the question carefully:

```bash
# Tab 1 (or background)
nmap -sV -sC -p- -T4 -oA full 10.10.10.5 &

# Tab 2
sudo nmap -sU --top-ports 20 -oN udp 10.10.10.5 &

# Tab 3 (assuming web ports likely)
nikto -h http://10.10.10.5 -o nikto.txt &
gobuster dir -u http://10.10.10.5 -w /usr/share/wordlists/dirb/common.txt -o gobuster.txt &
```

**While scans run**, you read the question carefully, plan your approach, check the repo. By the time you've decided what to do, scans have results ready.

---

## The "shared scan" trick

Multiple questions on same target = **one scan answers multiple questions**.

Example: Q3 asks for FTP version on 10.10.10.5, Q7 asks for SMB vuln on 10.10.10.5, Q11 asks for OS on 10.10.10.5.

**Bad approach (sequential):**
```bash
# Q3
nmap -sV -p 21 10.10.10.5    # 30s
# Q7
nmap -p 445 --script smb-vuln-* 10.10.10.5    # 1 min
# Q11
sudo nmap -O 10.10.10.5    # 30s
# Total: 2 min, plus 3 mental context switches
```

**Good approach (parallel + shared):**
```bash
# One mega scan, background
sudo nmap -sV -sC -O -p- --script "smb-vuln-*" -T4 -oA mega5 10.10.10.5 &
# 5 min later, all 3 answers in one file
grep -i ftp mega5.nmap     # Q3
grep -i ms17 mega5.nmap    # Q7
grep -i "OS details" mega5.nmap   # Q11
```

**Time saved: ~30 sec per question. Mental load saved: huge.**

---

## Multi-target parallel

3 different targets? 3 nmap scans in parallel:

```bash
nmap -sV -sC -p- -T4 -oA t5 10.10.10.5 &
nmap -sV -sC -p- -T4 -oA t20 10.10.10.20 &
nmap -sV -sC -p- -T4 -oA t40 10.10.10.40 &
```

All 3 will complete in ~5-7 min total (network + CPU permitting).

**While they run:** read questions, plan, take notes.

---

## Long-running tasks: hashcat & sqlmap

These can take 10+ min. Don't sit and watch.

### hashcat
```bash
hashcat -m 1000 hashes.txt /usr/share/wordlists/rockyou.txt -O &

# Check progress periodically
hashcat -m 1000 hashes.txt --show
```

### sqlmap dump
```bash
sqlmap -u "http://10.10.10.5/?id=1" --batch --dump-all -o &

# Check log
ls -la /root/.local/share/sqlmap/output/10.10.10.5/
```

---

## Workflow example: 1 hour of parallel work

```
Time 0:00
- Open 4 tabs
- Read Q3 (target 10.10.10.5)
- Tab 1: nmap mega scan on .5 → background
- Read Q5 (target 10.10.10.20)
- Tab 2: nmap mega scan on .20 → background
- Read Q7 (target 10.10.10.5 — same as Q3)
- Read Q9 (target 10.10.10.40, web app)
- Tab 3: nmap + nikto + gobuster on .40 → background

Time 0:05
- Tab 1 has results. Read for Q3 + Q7.
- Q3 = nmap output (FTP version)
- Q7 = nmap output (SMB vuln)
- Both done in 2 minutes from reading scan.

Time 0:10
- Tab 2 done. Read Q5.
- Q5 needs hash crack. Extract hash.
- Tab 4: hashcat hash → background
- Move to Q9 (web app)

Time 0:15
- Tab 3 results. Q9 needs sqlmap.
- Tab 4 still running hashcat
- Tab 1: sqlmap on Q9 url → background
- Move to Q11

...etc
```

**Result:** 4 questions in flight at all times. ~15 min per question average even though each *scan* takes 5+ min.

---

## Anti-patterns (DON'T do these)

### ❌ Watching nmap progress bar
Boring + waste. Background it.

### ❌ Re-running same scan
Use `-oA`, check file.

### ❌ Forgetting jobs in background
Use `jobs` periodically. Cleanup with `kill %N`.

### ❌ Confusing tabs
Label mentally. Use `pwd` + `ls` to orient if lost.

### ❌ Multiple sqlmaps on same DB
Slow + can lock you out. One at a time per DB.

---

## Mental discipline

Parallel work needs **mental tracking**. Use a notepad:
```
Tab 1: Q3+Q7 scan on .5 (DONE)
Tab 2: Q5 hash crack (RUNNING since 0:08)
Tab 3: Q9 web on .40 (RUNNING since 0:10)
Tab 4: free
```

Update every few minutes. Without this you'll lose track and re-do work.

---

## Prep practice

Yeh skill prep mein build karo:
1. Set up your terminal layout (tmux/terminator)
2. Practice background + jobs + fg commands
3. Practice running 3 nmaps + 1 sqlmap simultaneously without confusion
4. Time yourself — how fast can you get 3 targets scanned in parallel vs sequential?

Yeh single skill 1+ ghanta bachata hai exam mein. Worth practicing.
