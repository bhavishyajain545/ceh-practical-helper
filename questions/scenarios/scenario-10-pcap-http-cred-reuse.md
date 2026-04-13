# Scenario 10 — PCAP Credential Extraction to Service Reuse

**Difficulty:** 🟡 | **Time:** ~20 min | **Domains:** 07 Sniffing, 05 System Hacking
**Tools:** [wireshark](../../tools/wireshark.md), [tcpdump](../../tools/tcpdump.md), `ssh`

## Story
You're given `capture.pcap`. A user logged into an HTTP portal — reuse those creds to SSH into `10.10.10.88`.

## Step 1 — Overview the capture
```bash
tcpdump -nn -r capture.pcap | head -50
capinfos capture.pcap
```
**Expected finding:** traffic to port 80, some SSH noise.
**→ Tool ref:** [tcpdump](../../tools/tcpdump.md)

## Step 2 — Filter HTTP POSTs in Wireshark / tshark
```bash
tshark -r capture.pcap -Y 'http.request.method == "POST"' \
       -T fields -e http.host -e http.request.uri -e urlencoded-form.value
```
Or in Wireshark: `File > Open > capture.pcap` → filter `http.request.method == POST` → right-click a packet → `Follow > HTTP Stream`.
**Expected finding:** `POST /login.php` with body `user=mark&pass=Winter2024!`.
**→ Tool ref:** [wireshark](../../tools/wireshark.md)

## Step 3 — Also check Basic Auth headers
```bash
tshark -r capture.pcap -Y 'http.authorization' -T fields -e http.authorization
# decode base64:
echo 'bWFyazpXaW50ZXIyMDI0IQ==' | base64 -d
```
**Expected finding:** same creds via `Authorization: Basic ...`.

## Step 4 — SSH reuse
```bash
ssh mark@10.10.10.88
# password: Winter2024!
```

## Step 5 — Grab the flag
```bash
cat ~/user.txt
```

## Step 6 — Final answer
**Answer format:** username:password, or the flag, depending on the Q.

## Gotchas across this chain
- `Follow HTTP Stream` in Wireshark shows the body in human form — fastest for exam.
- Basic Auth is `base64(user:pass)` — always decode, never guess.
- If the PCAP has TLS, look for `ssl.handshake` SNI only; content needs keys (rarely in exam).
- `ftp` passwords are cleartext too — filter `ftp.request.command == "PASS"`.
- `telnet` keystrokes appear one char per packet — use `Follow TCP Stream` to reassemble.

## Variant questions this scenario teaches you to handle
- "Find the HTTP login credentials in the capture" → steps 2–3.
- "What URL was accessed?" → step 2.
- "Decode this base64 Authorization header" → step 3.
- "Get a shell on 10.10.10.88" → full chain.
