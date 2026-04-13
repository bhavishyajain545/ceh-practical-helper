# nmblookup — query NetBIOS names via Samba

> **Samba's NetBIOS lookup tool.** Resolves and reverses NetBIOS names, lists domain info.

**Install check:** `nmblookup -V` (samba-common-bin)

---

## 🎯 Cheat-flow

```bash
nmblookup -A 10.10.10.5                   # all names (node status) on a host
nmblookup WORKGROUP                       # who claims this name?
nmblookup '*'                              # broadcast — who's around
nmblookup -S <host>                       # node status query
nmblookup -T 10.10.10.5                   # reverse (name from IP)
nmblookup -M WORKGROUP                    # domain master browser
```

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-A <ip>` | Interpret as node status query (fetches all names) |
| `-S` | Also do node status |
| `-T` | Translate IPs to names |
| `-M` | Search for master browsers |
| `-R` | Set recursion desired bit |
| `-U <host>` | Unicast to specific WINS server |

---

## 📋 Typical output

```
$ nmblookup -A 10.10.10.5
Looking up status of 10.10.10.5
    DC01            <00> -         B <ACTIVE>    Workstation
    CORP            <00> - <GROUP> B <ACTIVE>    Domain
    DC01            <20> -         B <ACTIVE>    File Server
    CORP            <1C> - <GROUP> B <ACTIVE>    Domain Controllers
    CORP            <1B> -         B <ACTIVE>    Domain Master Browser
    MAC Address = 00-0C-29-AA-BB-CC
```

Reading suffixes: see [nbtscan](nbtscan.md) table. `<1C>` group members = domain controllers.

---

## ⚠️ Gotchas

- NetBIOS must not be blocked (UDP 137).
- Often returns the same info as `nmap --script nbstat -p 137 <IP>`.
- For richer enumeration use [enum4linux](enum4linux.md) which wraps nmblookup + rpcclient + smbclient.

---

## 🔗 Related

- [nbtscan](nbtscan.md) · [enum4linux](enum4linux.md) · [smbclient](smbclient.md) · [rpcclient](rpcclient.md)
