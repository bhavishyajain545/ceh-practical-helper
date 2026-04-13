# 08 — Social Engineering

> In CEH Practical this almost always means **SET (Social-Engineer Toolkit)**. Expect 1–2 questions asking you to clone a login page, harvest credentials, or generate a malicious payload via SET's infection vector.

## 🧭 Decision tree — "I see a social engineering question"

```
What do they want?
│
├── "Clone a website and capture credentials"
│   └── setoolkit
│       1) Social-Engineering Attacks
│       2) Website Attack Vectors
│       3) Credential Harvester Attack Method
│       2) Site Cloner
│       → enter attacker IP  → enter URL to clone
│       → victim submits form → creds printed in SET console
│
├── "Send a phishing email to many targets"
│   └── setoolkit
│       1) Social-Engineering Attacks
│       5) Mass Mailer Attack
│       → 1 (single email) or 2 (mass list)
│
├── "Deliver a payload via a cloned site" (drive-by)
│   └── setoolkit
│       1) Social-Engineering Attacks
│       2) Website Attack Vectors
│       1) Java Applet / 3) Metasploit Browser Exploit / Infectious Media
│
└── "USB / infectious media payload"
    └── setoolkit → 1 → 3) Infectious Media Generator
```

## 📄 Files in this folder

- **[commands.md](commands.md)** — copy-paste recipes
- **[walkthroughs.md](walkthroughs.md)** — full multi-step examples
- Question bank: **[../questions/by-domain/08-social-engineering.md](../questions/by-domain/08-social-engineering.md)**

## 🛠 Tools used in this domain

- **[setoolkit](../tools/setoolkit.md)** ← the only tool you really need
- **[credential-harvester](../tools/setoolkit.md#credential-harvester)** (SET submodule)
- **[mass-mailer](../tools/setoolkit.md#mass-mailer)** (SET submodule)

## ✅ Domain checklist

- [ ] Launch setoolkit and remember the menu path: `1 → 2 → 3 → 2`
- [ ] Clone a login page (facebook, gmail, or a lab target) and capture creds
- [ ] Find the `harvester_<timestamp>.txt` report file location
- [ ] Know your attacker IP and how the victim reaches it from the lab network
- [ ] Generate an infectious-media payload with SET
- [ ] Done all questions in [the question bank](../questions/by-domain/08-social-engineering.md)
