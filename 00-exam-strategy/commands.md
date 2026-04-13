# 00 Exam Strategy — the 5 first-moves

> Every question that gives you a target IP starts the same way. Muscle-memory these five commands. Full version: **[../exam-day/first-5-commands.md](../exam-day/first-5-commands.md)**

## Setup (run ONCE at exam start)

```bash
mkdir -p ~/exam && cd ~/exam
tmux new -s ceh
export TARGET=<IP>         # reset per question
```

## Move 1 — is it up?

```bash
ping -c 2 $TARGET
nmap -sn $TARGET             # ICMP-less ping sweep
```

→ If filtered, assume up and add `-Pn` everywhere.

## Move 2 — fast port sweep (30 sec answer)

```bash
nmap -sV --top-ports 1000 -T4 -oN quick.txt $TARGET
```

→ See [../02-scanning/commands.md](../02-scanning/commands.md)

## Move 3 — full scan in background

```bash
nmap -sV -sC -p- -T4 -oA full $TARGET &
```

Let this run while you answer from the quick scan.

## Move 4 — UDP top 20 (never skip)

```bash
sudo nmap -sU --top-ports 20 -oN udp.txt $TARGET
```

→ SNMP (161) and DNS (53) love to hide here.

## Move 5 — note what you see, pick a domain

```bash
grep "^[0-9]" quick.txt | tee services.txt
```

Now jump to the domain README:
- SMB/SNMP/LDAP/NFS open → [../03-enumeration/](../03-enumeration/README.md)
- HTTP(S) open → [../06-web/](../06-web/README.md) *(coming)*
- Only weird services → [../04-vuln-analysis/](../04-vuln-analysis/README.md)
- Need CVE per banner → [../04-vuln-analysis/commands.md](../04-vuln-analysis/commands.md)

## Time budget per question

```
 0:00  read question twice
 0:30  first 5 moves kicked off
 3:00  quick answer usually possible here
 8:00  full scan done, deeper enum
15:00  FLAG and move on if still stuck
```

## Copy-answer checklist

```bash
# Before submitting any text answer:
echo -n "<ANSWER>" | wc -c     # sanity-check length
# Watch for trailing spaces, capitalization, dashes vs spaces
```
