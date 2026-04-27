# Q198 — Extract Email Content from SMTP Traffic

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Sniffing / Packet Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `wireshark, tshark` |
| **Time budget** | 8–12 min |

---

## 📝 Question

"A `smtp_capture.pcap` contains an email sent via SMTP. Extract the sender, recipient, subject, and message body. Find the flag in the email body."

---

## 🎯 Flag Format

```
from=<email>; to=<email>; flag=<string>
```

Example: `from=user@test.com; to=admin@test.com; flag=EMAIL_FLAG`

---

## 💡 Hints

**Hint 1**

Filter: `smtp` → Follow TCP Stream to see full SMTP conversation.

**Hint 2**

SMTP commands: `MAIL FROM:`, `RCPT TO:`, `DATA` section has the body.

---

## ✅ Solution

```bash
tshark -r smtp_capture.pcap -z follow,tcp,ascii,0 -q
```

Look for `MAIL FROM:`, `RCPT TO:`, and message body after `DATA`.

**Answer:** `from=hacker@lab.com; to=admin@lab.com; flag=SMTP_INTERCEPTED`

---

## 🤖 Claude Setup Prompt

```bash
tcpdump -i eth0 port 25 -w smtp_capture.pcap &
# Send email via SMTP
(echo "HELO lab.com"; sleep 1; echo "MAIL FROM:<hacker@lab.com>"; sleep 1; echo "RCPT TO:<admin@lab.com>"; sleep 1; echo "DATA"; sleep 1; echo "Subject: Secret Flag"; echo ""; echo "The flag is SMTP_INTERCEPTED"; echo "."; sleep 1; echo "QUIT") | nc 192.168.52.129 25
sleep 2 && kill %1
```

Report back: "Lab ready for Q198 — smtp_capture.pcap with email traffic ready"
