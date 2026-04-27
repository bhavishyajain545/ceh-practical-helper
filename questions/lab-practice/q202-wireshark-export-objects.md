# Q202 — Export HTTP Objects from PCAP

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Sniffing / Packet Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `wireshark, tshark` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"Open `web_traffic.pcap` in Wireshark. Export all HTTP objects (files downloaded). How many files were transferred? Find the flag in one of the downloaded files."

---

## 🎯 Flag Format

```
files=<number>; flag=<string>
```

Example: `files=3; flag=HTTP_OBJECT_FLAG`

---

## 💡 Hints

**Hint 1**

Wireshark: File → Export Objects → HTTP

**Hint 2**

`tshark -r web_traffic.pcap --export-objects http,exported_files/`

---

## ✅ Solution

```bash
mkdir exported_files
tshark -r web_traffic.pcap --export-objects http,exported_files/
ls exported_files/
cat exported_files/*.txt
```

**Answer:** `files=3; flag=HTTP_EXPORTED_FLAG`

---

## 🤖 Claude Setup Prompt

```bash
# Create web content with flag
echo "HTTP_EXPORTED_FLAG" > /var/www/html/flag.txt
tcpdump -i eth0 port 80 -w web_traffic.pcap &
curl http://192.168.52.129/flag.txt
curl http://192.168.52.129/index.html
curl http://192.168.52.129/robots.txt
sleep 3 && kill %1
```

Report back: "Lab ready for Q202 — web_traffic.pcap with downloadable HTTP objects ready"
