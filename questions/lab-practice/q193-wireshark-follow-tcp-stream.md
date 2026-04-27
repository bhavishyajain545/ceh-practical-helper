# Q193 — Follow TCP Stream to Extract Conversation

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Sniffing / Packet Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `wireshark, tshark` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"Open `session.pcap` and follow TCP stream 0. Extract the full conversation. What service is being used and what credentials were sent?"

---

## 🎯 Flag Format

```
service=<name>; user=<username>; pass=<password>
```

Example: `service=ftp; user=admin; pass=secret`

---

## 💡 Hints

**Hint 1**

Wireshark: Right-click packet → Follow → TCP Stream

**Hint 2**

`tshark -r session.pcap -z follow,tcp,ascii,0`

---

## ✅ Solution

```bash
tshark -r session.pcap -z follow,tcp,ascii,0 -q
```

**Answer:** `service=ftp; user=msfadmin; pass=msfadmin`

---

## 🤖 Claude Setup Prompt

```bash
tcpdump -i eth0 -w session.pcap &
# Generate FTP session
ftp -n 192.168.52.129 << FTPCMDS
user msfadmin msfadmin
ls
quit
FTPCMDS
sleep 2 && kill %1
```

Report back: "Lab ready for Q193 — session.pcap with FTP conversation ready"
