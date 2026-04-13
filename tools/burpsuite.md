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

## 🔗 Related

- [ffuf](ffuf.md) · [hydra](hydra.md) · [sqlmap](sqlmap.md) · [zap](zap.md) · [nikto](nikto.md)
- [Web app playbook](../playbooks/webapp-playbook.md)
