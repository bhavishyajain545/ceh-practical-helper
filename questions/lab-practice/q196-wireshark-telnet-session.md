# Q196 — Reconstruct Telnet Session from PCAP

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Sniffing / Packet Analysis |
| **Difficulty** | 🟡 Medium |
| **Tools** | `wireshark, tshark` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"A `telnet.pcap` file contains a captured Telnet session. Reconstruct the session to find the login credentials and any commands executed. Report the username, password, and last command run."

---

## 🎯 Flag Format

```
user=<username>; pass=<password>; cmd=<last_command>
```

Example: `user=root; pass=toor; cmd=whoami`

---

## 💡 Hints

**Hint 1**

Filter: `telnet` → Follow TCP Stream to see full session.

**Hint 2**

Telnet mein har character individually send hota hai — TCP stream mein readable format mein dikhega.

---

## ✅ Solution

```bash
tshark -r telnet.pcap -z follow,tcp,ascii,0 -q
```

**Answer:** `user=msfadmin; pass=msfadmin; cmd=cat /etc/passwd`

---

## 🤖 Claude Setup Prompt

```bash
tcpdump -i eth0 port 23 -w telnet.pcap &
# Telnet session
(echo msfadmin; sleep 1; echo msfadmin; sleep 1; echo "cat /etc/passwd"; sleep 1; echo exit) | telnet 192.168.52.129
sleep 2 && kill %1
```

Report back: "Lab ready for Q196 — telnet.pcap with Telnet session ready"
