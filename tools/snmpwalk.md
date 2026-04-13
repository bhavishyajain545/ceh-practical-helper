# snmpwalk — SNMP enumeration

> **UDP/161.** If SNMP is open with `public` community, it's a goldmine: running processes, installed software, users, routes, ARP tables. CEH loves SNMP questions.

**Install check (Parrot — already installed):** `snmpwalk --version`

---

## 🎯 Cheat-flow: "What do I run?"

| You need to find... | Run this | OID |
|---|---|---|
| **Everything** (public v1) | `snmpwalk -c public -v 1 <IP>` | `.1` |
| Everything (v2c) | `snmpwalk -c public -v 2c <IP>` | `.1` |
| **Running processes** | `snmpwalk -c public -v 1 <IP> 1.3.6.1.2.1.25.4.2.1.2` | hrSWRunName |
| **Installed software** | `snmpwalk -c public -v 1 <IP> 1.3.6.1.2.1.25.6.3.1.2` | hrSWInstalledName |
| **Users** (Windows) | `snmpwalk -c public -v 1 <IP> 1.3.6.1.4.1.77.1.2.25` | LanMgr users |
| **Open TCP ports** | `snmpwalk -c public -v 1 <IP> 1.3.6.1.2.1.6.13.1.3` | tcpConnLocalPort |
| **Open UDP ports** | `snmpwalk -c public -v 1 <IP> 1.3.6.1.2.1.7.5.1.2` | udpLocalPort |
| **Network interfaces** | `snmpwalk -c public -v 1 <IP> 1.3.6.1.2.1.2.2.1.2` | ifDescr |
| **Routing table** | `snmpwalk -c public -v 1 <IP> 1.3.6.1.2.1.4.21.1.1` | ipRouteDest |
| **ARP / MACs** | `snmpwalk -c public -v 1 <IP> 1.3.6.1.2.1.4.22.1.2` | ipNetToMediaPhysAddress |
| **System description** | `snmpwalk -c public -v 1 <IP> 1.3.6.1.2.1.1.1` | sysDescr |
| **Hostname** | `snmpwalk -c public -v 1 <IP> 1.3.6.1.2.1.1.5` | sysName |
| **Shares** (Windows) | `snmpwalk -c public -v 1 <IP> 1.3.6.1.4.1.77.1.2.27` | Shares |

---

## 🔑 Flags you must know cold

| Flag | Meaning |
|---|---|
| `-c <community>` | Community string (`public`, `private`) |
| `-v 1` / `-v 2c` / `-v 3` | SNMP version |
| `-On` | Numeric OIDs (don't resolve names) |
| `-Oa` | ASCII string output |
| `-Of` | Full OID path |
| `-Os` | Short OID names |
| `-t <sec>` | Timeout (default 1) |
| `-r <n>` | Retries |
| `-Cc` | Don't stop on lexicographic errors |

### SNMPv3 auth
| Flag | Meaning |
|---|---|
| `-l authPriv` | Security level |
| `-u <user>` | Username |
| `-a SHA` `-A <pass>` | Auth algo + pass |
| `-x AES` `-X <pass>` | Priv algo + pass |

---

## 📋 Key OIDs cheat sheet

```
1.3.6.1.2.1.1.1.0               sysDescr (OS version)
1.3.6.1.2.1.1.5.0               sysName (hostname)
1.3.6.1.2.1.25.1.6.0            hrSystemProcesses (count)
1.3.6.1.2.1.25.4.2.1.2          hrSWRunName (processes)
1.3.6.1.2.1.25.6.3.1.2          hrSWInstalledName (software)
1.3.6.1.2.1.2.2.1.2             ifDescr (interfaces)
1.3.6.1.2.1.6.13.1.3            tcpConnLocalPort
1.3.6.1.2.1.7.5.1.2             udpLocalPort
1.3.6.1.2.1.4.21.1.1            ipRouteDest
1.3.6.1.2.1.4.22.1.2            ipNetToMediaPhysAddress (ARP)
1.3.6.1.4.1.77.1.2.25           Windows users (LanMgr)
1.3.6.1.4.1.77.1.2.27           Windows shares
1.3.6.1.4.1.77.1.4.1            Windows domain
```

---

## 📋 Command recipes (copy-paste)

```bash
# 1. Full walk (default first move)
snmpwalk -c public -v 1 <IP> | tee snmp.txt
snmpwalk -c public -v 2c <IP> | tee snmp.txt

# 2. Brute the community string first (if public fails)
onesixtyone -c /usr/share/seclists/Discovery/SNMP/common-snmp-community-strings.txt <IP>

# 3. Running processes
snmpwalk -c public -v 1 <IP> 1.3.6.1.2.1.25.4.2.1.2

# 4. Installed software
snmpwalk -c public -v 1 <IP> 1.3.6.1.2.1.25.6.3.1.2

# 5. Windows users
snmpwalk -c public -v 1 <IP> 1.3.6.1.4.1.77.1.2.25

# 6. Grab everything to parse later
snmpwalk -c public -v 2c -On <IP> .1 > full.txt

# 7. snmp-check (nicer wrapper)
snmp-check -c public -v 1 <IP>

# 8. Nmap NSE for SNMP
nmap -sU -p 161 --script "snmp-*" <IP>
```

---

## ⚠️ Gotchas

- **SNMP is UDP/161** — `nmap -sU` is needed to see it. It's easy to miss.
- **Try v1 AND v2c.** Some agents only speak one. v3 needs user creds.
- **Community strings:** `public` (read), `private` (write). Always try both.
- **Answer format:** when asked for a process/service, copy the exact string as snmpwalk prints it.
- **Walks are SLOW** — default timeout is 1 sec. Add `-t 5 -r 2` for flaky targets.
- **If walk hangs at an OID**, use `-Cc` to skip lexicographic errors.
- **Use `-On`** when you need the numeric OID (exam sometimes asks for it literally).
- **onesixtyone** brute-forces community strings fast if `public` fails.

---

## 🔗 Related

- [nmap](./nmap.md) — `-sU -p 161 --script snmp-*`
- [playbooks/enumeration-playbook](../playbooks/enumeration-playbook.md)
- `onesixtyone` — community string brute
- `snmp-check` — nicer auto-walker
