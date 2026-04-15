# burpsuite — the web pentester's IDE

> **The go-to web app proxy for CEH Practical.** If a question involves HTTP requests, tampering, or web auth, Burp is almost always the answer.

**Launch:** `burpsuite &` (Community edition ships on Parrot)

---

## 🎯 Cheat-flow: "Which Burp tool do I use?"

| You need to... | Use | How |
|---|---|---|
| See every request the browser makes | **Proxy → HTTP history** | Intercept off, browse through Burp |
| Modify one request and replay | **Repeater** | Right-click request → Send to Repeater (Ctrl+R) |
| Brute-force a login / fuzz a param | **Intruder** | Send to Intruder (Ctrl+I) |
| Decode base64 / URL / hex | **Decoder** | Paste, pick decoder |
| Test session token randomness | **Sequencer** | Capture tokens, analyze |
| Find vulns automatically (Pro only) | **Scanner** | Right-click → Scan |
| Compare two responses | **Comparer** | Send to Comparer |

---

## 🔑 Proxy setup (do this first)

1. **Proxy → Options** → listener on `127.0.0.1:8080` (default).
2. Browser: set HTTP/HTTPS proxy to `127.0.0.1:8080`. Or use FoxyProxy.
3. Install CA: browse to `http://burp` → download `cacert.der` → import into browser trust store.
4. **Proxy → Intercept** → toggle ON/OFF with the button (OFF for passive browsing).

---

## 🧪 Repeater (most used)

- `Ctrl+R` — send current request to Repeater
- `Ctrl+Space` — send (in Repeater tab)
- Edit any header/body, click **Send**, see response instantly.
- Perfect for: SQLi tests, XSS probes, auth bypass, verb tampering.

---

## 🧪 Intruder — attack types

| Type | Use |
|---|---|
| **Sniper** | One payload set, one position at a time — default for fuzzing single param |
| **Battering ram** | Same payload, all positions at once |
| **Pitchfork** | Multiple payload sets in parallel (user1→pw1, user2→pw2) |
| **Cluster bomb** | Cartesian product — **username + password brute force** |

**Workflow:**
1. Capture login POST in Proxy.
2. Ctrl+I → Intruder → **Positions** tab.
3. Clear `§` markers, highlight user + pass values, click **Add §**.
4. Attack type: **Cluster bomb**.
5. **Payloads** tab: set payload set 1 = usernames, set 2 = passwords.
6. **Start attack**. Sort by Length/Status — the odd one is the hit.

---

## 🧪 Decoder

Paste ciphertext → **Decode as** → URL / HTML / Base64 / ASCII hex / Hex / Octal / Binary / Gzip. Chain multiple decoders on the same string.

**Smart decode** auto-guesses the encoding — great for CTF-style questions.

---

## 🧪 Sequencer

Use when a question asks "is this session token random/predictable?"

1. Capture a response that sets a cookie.
2. Send to Sequencer.
3. Select the token, **Start live capture** (needs ~100–20,000 samples).
4. Look at **entropy** — low bits = predictable.

---

## 📋 Common recipes

```text
# 1. Intercept a single login
Proxy → Intercept ON → submit form → see request → Forward

# 2. Brute force login
Ctrl+I → cluster bomb → payloads → Start attack → sort by Length

# 3. Change verb (GET→PUT) for method tampering
Ctrl+R → change first line → Send

# 4. Replay with tampered cookie
Ctrl+R → edit Cookie: header → Send

# 5. Save session
Project menu → Save copy of project
```

---

## ⚠️ Gotchas

- **HTTPS errors?** You forgot to install the Burp CA cert in the browser.
- **Nothing in HTTP history?** Intercept is ON and you haven't forwarded. Turn it OFF for passive capture.
- **Scope** — set target scope (Target → Scope) so you only see traffic that matters.
- **Community edition** has NO active scanner and throttled Intruder — for brute force consider [hydra](hydra.md) or [ffuf](ffuf.md) instead.
- **Answers** — when a question asks "what value does the server return?" always check Repeater response tab, not Proxy.

---

## 🧩 Extender / BApp Store (extensions you'll actually use)

**Extender tab → BApp Store** — free extensions. Top picks for CEH / pentest work:

| Extension | Purpose |
|---|---|
| **JWT Editor** | Inspect, sign, crack JWTs right in Repeater (alg=none, key confusion, HS256↔RS256 swap) |
| **Autorize** | Automatic broken-access-control testing — replays each request as another user |
| **Logger++** | Advanced request log with regex filters, columns, CSV export |
| **Turbo Intruder** | Python-scripted Intruder replacement — race conditions, 1000s of reqs/sec |
| **Hackvertor** | Inline payload tagging/encoding (`<@base64>...<@/base64>`) |
| **Param Miner** | Find hidden parameters and cache-poisoning keys |
| **Backslash Powered Scanner** | Next-gen injection scanner |
| **Active Scan++** | Extends the built-in scanner with more checks |
| **Upload Scanner** | File-upload vuln testing |
| **Collaborator Everywhere** | OOB / SSRF probe injection |

Install: Extender → BApp Store → click **Install**. Some require Jython (Python) or JRuby — set in Extender → Options.

### JWT Editor walkthrough (scenario-15 pattern)

1. Capture a request bearing a JWT in `Authorization: Bearer ...`
2. Send to Repeater
3. Bottom tab → **JSON Web Token** (added by JWT Editor)
4. Attack options:
   - **alg=none attack** — change header `"alg":"none"`, drop signature → send; if server accepts, you can forge any payload
   - **Weak secret** — export JWT, crack with hashcat: `hashcat -m 16500 jwt.txt rockyou.txt`
   - **Key confusion (RS256→HS256)** — grab server's public key, sign with HS256 using that key as the secret
   - **Embedded JWK** (`jwk` header) — inject your own key
   - **kid manipulation** — path traversal / SQLi in `kid`
5. Send, observe — if status 200 with tampered role/uid → vuln confirmed

---

## 🔀 Match-and-Replace (Proxy → Options → Match and Replace)

Rewrite any request/response automatically — great for:
- Stripping `HSTS` headers
- Force `User-Agent: Mozilla/5.0 ...`
- Always replace `role=user` → `role=admin` in outgoing requests
- Inject XSS/SQLi payloads into every instance of a parameter

**Enable a rule:** check the box next to it. **Add rule:** specify type (Request header, Request body, Response header, ...), match regex, replace with string.

---

## 🔁 Macros + Session Handling Rules

For **authenticated scans** where each request needs a fresh CSRF token or session cookie refresh.

1. **Project options → Sessions → Macros → Add**
   - Record a sequence (e.g. GET /login, POST /login)
   - Define what to extract from responses (CSRF token from HTML)
2. **Session Handling Rule → Add**
   - Scope: which tools (Scanner, Intruder, Repeater...)
   - Action: **Run a macro** → pick the macro
   - Action: **Check session is valid** → regex on response
   - Action: **Update current request with params from response** (inject the fresh CSRF)
3. Now Intruder/Scanner auto-refreshes auth between every request

This is how you run Burp Scanner behind logins that expire — CEH rarely drills macros but they're in the blueprint.

---

## 🎯 Intruder attack types — when to use each

| Type | Payload sets | Use case |
|---|---|---|
| **Sniper** | 1 | Fuzzing one parameter — default for SQLi/XSS probing |
| **Battering ram** | 1 (repeated across all positions) | Same payload in multiple spots simultaneously (e.g. test both user+pass with "admin") |
| **Pitchfork** | N (one per position, parallel) | **Username:password combo list** (user1→pw1, user2→pw2) |
| **Cluster bomb** | N (cartesian product) | **Brute force** — every username × every password |

Pick by remembering: one payload = Sniper, parallel pairs = Pitchfork, every combination = Cluster bomb.

---

## 🔗 Related

- [ffuf](ffuf.md) · [hydra](hydra.md) · [sqlmap](sqlmap.md) · [zap](zap.md) · [nikto](nikto.md)
- [Web app playbook](../playbooks/webapp-playbook.md)
