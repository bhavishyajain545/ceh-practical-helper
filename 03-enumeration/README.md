# 03 — Enumeration

> Active information gathering **after** scanning. You know ports are open — now extract users, shares, community strings, OUs, exports. Expect 3–5 questions. SMB and SNMP are the biggest point earners.

## 🧭 Decision tree — "scan is done, what do I enumerate?"

```
Which port/service is open?
│
├── 139 / 445 — SMB / NetBIOS
│   ├── enum4linux-ng -A <IP>          ← run this first, it does everything
│   ├── smbclient -L //<IP>/ -N        ← list shares (null session)
│   ├── smbmap -H <IP>                 ← shares + perms
│   ├── nbtscan <RANGE>                ← NetBIOS names
│   └── rpcclient -U "" -N <IP>        ← "enumdomusers", "querydominfo"
│
├── 161 UDP — SNMP
│   ├── onesixtyone -c community.txt <IP>   ← find the string first
│   ├── snmpwalk -v2c -c <COMMUNITY> <IP>
│   └── snmp-check <IP> -c <COMMUNITY>
│
├── 389 / 636 — LDAP
│   ├── ldapsearch -x -H ldap://<IP> -s base namingcontexts
│   └── ldapsearch -x -H ldap://<IP> -b "<BASE-DN>"
│
├── 2049 — NFS
│   ├── showmount -e <IP>              ← list exports
│   └── mount -t nfs <IP>:/share /mnt
│
├── 25 — SMTP
│   └── smtp-user-enum -M VRFY -U users.txt -t <IP>
│
├── 53 — DNS
│   └── → see [../01-recon-footprinting](../01-recon-footprinting/README.md)
│
└── 79 — finger
    └── finger @<IP>  /  finger user@<IP>
```

## 📄 Files in this folder

- **[commands.md](commands.md)** — copy-paste recipes
- **[walkthroughs.md](walkthroughs.md)** — full multi-step examples
- Question bank: **[../questions/03-enumeration.md](../questions/03-enumeration.md)** *(coming)*

## 🛠 Tools used in this domain

- **[enum4linux](../tools/enum4linux.md)** — the SMB silver bullet
- **[smbclient](../tools/smbclient.md)** — SMB CLI (Samba)
- **[smbmap](../tools/smbmap.md)** — SMB share permissions
- **[rpcclient](../tools/rpcclient.md)** — RPC over SMB (user enum)
- **[nbtscan](../tools/nbtscan.md)** — NetBIOS name scanner
- **[snmpwalk](../tools/snmpwalk.md)** — SNMP tree walker
- **[onesixtyone](../tools/onesixtyone.md)** — SNMP community bruteforce
- **[ldapsearch](../tools/ldapsearch.md)** — LDAP queries
- **[showmount](../tools/showmount.md)** — NFS exports
- **[finger](../tools/finger.md)** — user enum (legacy)

## ✅ Domain checklist

- [ ] Null-session enum on SMB with `enum4linux-ng -A <IP>` and read every section
- [ ] List SMB shares + permissions with `smbmap -H <IP>`
- [ ] Brute-force SNMP community with `onesixtyone` using `/usr/share/doc/onesixtyone/dict.txt`
- [ ] Walk full SNMP tree with `snmpwalk -v2c -c public`
- [ ] Pull LDAP naming contexts anonymously
- [ ] List NFS exports and mount a world-readable share
- [ ] Know default SNMP strings: `public`, `private`, `community`, `manager`
- [ ] Recognize when a question needs the **domain SID** (`rpcclient → lsaquery`)
