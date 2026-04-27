# Q195 — Extract FTP Credentials from PCAP

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Sniffing / Packet Analysis |
| **Difficulty** | 🟢 Easy |
| **Tools** | `wireshark, tshark` |
| **Time budget** | 5–8 min |

---

## 📝 Question

"Open `ftp_capture.pcap`. FTP sends credentials in plaintext. Find the USER and PASS commands. Report the credentials."

---

## 🎯 Flag Format

```
user=<username>; pass=<password>
```

Example: `user=ftpuser; pass=ftppass`

---

## 💡 Hints

**Hint 1**

Filter: `ftp` ya `ftp.request.command == USER || ftp.request.command == PASS`

**Hint 2**

`tshark -r ftp_capture.pcap -Y "ftp.request.command==USER || ftp.request.command==PASS"`

---

## ✅ Solution

```bash
tshark -r ftp_capture.pcap -Y "ftp.request.command==USER || ftp.request.command==PASS" -T fields -e ftp.request.arg
```

**Answer:** `user=msfadmin; pass=msfadmin`

---

## 🤖 Claude Setup Prompt

```bash
tcpdump -i eth0 port 21 -w ftp_capture.pcap &
ftp -n 192.168.52.129 << FTP
user msfadmin msfadmin
quit
FTP
sleep 2 && kill %1
```

Report back: "Lab ready for Q195 — ftp_capture.pcap with FTP credentials ready"
