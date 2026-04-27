# 🔓 SMTP PORT OPEN (25) — Ab Kya?

---

## CASE 1: User Enumeration (Sabse Common)
```bash
smtp-user-enum -M VRFY -U /usr/share/wordlists/metasploit/unix_users.txt -t <IP>
smtp-user-enum -M EXPN -U users.txt -t <IP>
smtp-user-enum -M RCPT -U users.txt -t <IP>
nmap --script smtp-enum-users -p 25 <IP>
```
> Users mile? → SSH/FTP pe brute force karo in usernames se

---

## CASE 2: Manual SMTP Interaction
```bash
nc <IP> 25
HELO test.com
VRFY admin
VRFY root
VRFY msfadmin
MAIL FROM:<hacker@test.com>
RCPT TO:<admin@target.com>
```

---

## CASE 3: SMTP Open Relay Check
```bash
nmap --script smtp-open-relay -p 25 <IP>
```
> Open relay = spam bhej sakte ho (exam mein rarely poocha jaata hai)

---

## CASE 4: Email Sniffing (PCAP mein)
```bash
# Wireshark filter:
smtp
# Follow TCP Stream → email content dikhega
tshark -r capture.pcap -Y "smtp" -z follow,tcp,ascii,0 -q
```

---

## QUICK DECISION:
```
SMTP open (25)
  ├─ User enumeration: smtp-user-enum -M VRFY
  ├─ Manual: nc <IP> 25 → VRFY username
  ├─ Open relay check: nmap script
  └─ PCAP mein email? → Follow TCP Stream
```
