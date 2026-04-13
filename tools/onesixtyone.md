# onesixtyone — fast SNMP community brute

> **Blazing-fast SNMP community string guesser.** Sends UDP queries in parallel — tests thousands of strings per second.

**Install check:** `onesixtyone --help`

---

## 🎯 Cheat-flow

```bash
onesixtyone 10.10.10.5 public                     # single community
onesixtyone -c community.txt 10.10.10.5           # string list
onesixtyone -c community.txt -i hosts.txt         # list of hosts
onesixtyone -c community.txt -i hosts.txt -o out.txt
onesixtyone -w 10 -c community.txt 10.10.10.5     # 10ms wait between packets
```

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-c <file>` | Community strings file (one per line) |
| `-i <file>` | Host list |
| `-o <file>` | Output |
| `-w <ms>` | Wait between packets (default 10) |
| `-q` | Quiet |
| `-d` | Debug |

Common wordlist: `/usr/share/wordlists/metasploit/snmp_default_pass.txt` or roll your own:
```
public
private
cisco
manager
admin
read
write
0
```

---

## 📋 Recipes

```bash
# 1. Quick top-6 communities against a host
printf 'public\nprivate\ncisco\nmanager\nread\nwrite\n' > comm.txt
onesixtyone -c comm.txt 10.10.10.5

# 2. Subnet sweep
nmap -sU -p 161 --open -oG - 10.10.10.0/24 | awk '/Up/{print $2}' > up.txt
onesixtyone -c comm.txt -i up.txt

# 3. Follow up with snmpwalk on a hit
snmpwalk -v2c -c public 10.10.10.5
```

---

## ⚠️ Gotchas

- UDP/161 — lossy transport. Rerun to confirm hits.
- SNMPv3 uses auth — onesixtyone can't do v3.
- Too-fast scans drop packets on small devices; raise `-w`.
- Once you have the community, pivot to [snmpwalk](snmpwalk.md) for real enumeration.

---

## 🔗 Related

- [snmpwalk](snmpwalk.md) · [nmap](nmap.md) (`snmp-brute`, `snmp-info`)
