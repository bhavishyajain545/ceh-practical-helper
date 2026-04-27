# 🔓 SNMP PORT OPEN (161 UDP) — Ab Kya?

---

## CASE 1: Default Community String Try Karo
```bash
snmpwalk -v2c -c public <IP>
snmpwalk -v2c -c private <IP>
onesixtyone -c /usr/share/wordlists/seclists/Discovery/SNMP/common-snmp-community-strings.txt <IP>
nmap -sU -p 161 --script snmp-brute <IP>
```

---

## CASE 2: System Info Nikalo
```bash
snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.1         # system info
snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.1.1.0     # sysDescr
snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.1.5.0     # hostname
```

---

## CASE 3: Users / Processes / Software
```bash
# Running processes
snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.25.4.2.1.2

# Installed software
snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.25.6.3.1.2

# User accounts
snmpwalk -v2c -c public <IP> 1.3.6.1.4.1.77.1.2.25

# Network interfaces
snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.2.2.1.2

# Open TCP ports
snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.6.13.1.3
```

---

## CASE 4: Nmap SNMP Scripts
```bash
nmap -sU -p 161 --script snmp-info,snmp-processes,snmp-interfaces <IP>
nmap -sU -p 161 --script snmp-win32-users <IP>    # Windows users
nmap -sU -p 161 --script snmp-win32-software <IP>  # installed software
```

---

## KEY POINT:
> SNMP = information goldmine. Exam mein mostly **enumerate karo** type question hota hai
> Community string `public` almost always kaam karta hai exam mein

---

## QUICK DECISION:
```
SNMP open (161 UDP)
  ├─ Community string: public / private
  ├─ Brute force: onesixtyone
  ├─ System info: sysDescr, hostname
  ├─ Users: OID 1.3.6.1.4.1.77.1.2.25
  ├─ Processes: OID 1.3.6.1.2.1.25.4.2.1.2
  └─ Installed software: OID 1.3.6.1.2.1.25.6.3.1.2
```
