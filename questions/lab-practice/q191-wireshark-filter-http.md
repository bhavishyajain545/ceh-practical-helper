# Q191 — Filter HTTP Traffic in Wireshark

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Sniffing / Packet Analysis |
| **Difficulty** | 🟢 Easy |
| **Tools** | `wireshark, tshark` |
| **Time budget** | 5–8 min |

---

## 📝 Question

"Open `capture.pcap` in Wireshark. Apply an HTTP filter and find the first GET request URL. Report the full URL path."

---

## 🎯 Flag Format

```
url=<path>; method=<GET|POST>
```

Example: `url=/admin/login.php; method=GET`

---

## 💡 Hints

**Hint 1**

Display filter: `http` ya `http.request`

**Hint 2**

`tshark -r capture.pcap -Y "http.request" -T fields -e http.request.uri`

---

## ✅ Solution

```bash
tshark -r capture.pcap -Y "http.request.method==GET" -T fields -e http.host -e http.request.uri | head -1
```
Or Wireshark: Filter `http.request` → check first GET packet.

**Answer:** `url=/secret/flag.html; method=GET`

---

## 🤖 Claude Setup Prompt

1. Generate pcap with HTTP traffic:
```bash
# Start tcpdump on Parrot
tcpdump -i eth0 -w /tmp/capture.pcap &
# Generate HTTP requests
curl http://192.168.52.129/secret/flag.html
curl http://192.168.52.129/index.html
sleep 2 && kill %1
cp /tmp/capture.pcap capture.pcap
```

Report back: "Lab ready for Q191 — capture.pcap with HTTP traffic ready"
