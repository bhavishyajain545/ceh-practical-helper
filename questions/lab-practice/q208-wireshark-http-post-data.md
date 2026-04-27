# Q208 — Extract POST Form Data from HTTP

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Sniffing / Packet Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `wireshark, tshark` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"Analyze `http_post.pcap`. Find the HTTP POST request and extract the form data submitted. What username and password were sent in the POST body?"

---

## 🎯 Flag Format

```
user=<username>; pass=<password>
```

---

## 💡 Hints

**Hint 1**

Filter: `http.request.method == POST`

**Hint 2**

`tshark -r http_post.pcap -Y "http.request.method==POST" -T fields -e urlencoded-form.key -e urlencoded-form.value`

---

## ✅ Solution

```bash
tshark -r http_post.pcap -Y "http.request.method==POST" -T fields -e urlencoded-form.key -e urlencoded-form.value
# Or follow TCP stream
tshark -r http_post.pcap -Y "http.request.method==POST" -z follow,tcp,ascii,0 -q
```

**Answer:** `user=admin; pass=password123`

---

## 🤖 Claude Setup Prompt

```bash
tcpdump -i eth0 port 80 -w http_post.pcap &
curl -X POST http://192.168.52.129/dvwa/login.php -d "username=admin&password=password123&Login=Login"
sleep 2 && kill %1
```

Report back: "Lab ready for Q208 — http_post.pcap with POST data ready"
