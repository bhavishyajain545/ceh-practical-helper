# Q194 — Extract HTTP Basic Auth Credentials

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Sniffing / Packet Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `wireshark, tshark` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"A pcap `http_auth.pcap` contains HTTP Basic Authentication traffic. Extract the credentials sent in the Authorization header. The credentials are Base64 encoded."

---

## 🎯 Flag Format

```
user=<username>; pass=<password>
```

Example: `user=admin; pass=password123`

---

## 💡 Hints

**Hint 1**

Filter: `http.authorization` — Authorization header milega.

**Hint 2**

Base64 decode karo: `echo "base64_string" | base64 -d`

---

## ✅ Solution

```bash
tshark -r http_auth.pcap -Y "http.authorization" -T fields -e http.authorization
# Output: Basic YWRtaW46cGFzc3dvcmQ=
echo "YWRtaW46cGFzc3dvcmQ=" | base64 -d
# Output: admin:password
```

**Answer:** `user=admin; pass=password`

---

## 🤖 Claude Setup Prompt

```bash
tcpdump -i eth0 -w http_auth.pcap &
curl -u admin:password http://192.168.52.129/restricted/
sleep 2 && kill %1
```

Report back: "Lab ready for Q194 — http_auth.pcap with Basic Auth traffic ready"
