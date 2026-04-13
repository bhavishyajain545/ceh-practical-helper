# nbtscan — scan NetBIOS name service

> **Quick LAN discovery of Windows / Samba hosts.** Queries UDP/137 for NetBIOS name, user, MAC.

**Install check:** `nbtscan -h`

---

## 🎯 Cheat-flow

```bash
nbtscan 10.10.10.0/24                  # whole subnet
nbtscan -r 10.10.10.0/24               # use local source port 137 (more reliable)
nbtscan -v 10.10.10.5                  # verbose — show all names
nbtscan -f 10.10.10.0/24               # full NBT response
nbtscan -s : 10.10.10.0/24             # colon-separated output
nbtscan -q 10.10.10.0/24               # quiet
nbtscan -m 10.10.10.0/24               # MAC addresses only
```

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-r` | Bind source port 137 (better for some firewalls) |
| `-v` | Verbose |
| `-f` | Full output with all NetBIOS names |
| `-H` | Print hostname column |
| `-s <sep>` | Output separator |
| `-t <sec>` | Timeout |
| `-b <N>` | Retries |
| `-m` | Show MAC |

---

## 📋 Output

```
IP address       NetBIOS Name     Server    User             MAC address
10.10.10.5       WKSTATION01      <server>  WKSTATION01      00-0c-29-aa-bb-cc
10.10.10.6       DC01             <server>  DC01             00-0c-29-dd-ee-ff
```

`<server>` column means the host shares something (file/print).

NetBIOS name suffixes:
- `<00>` — workstation
- `<03>` — messenger
- `<20>` — file server
- `<1B>` — domain master browser
- `<1C>` — domain controllers (group)

---

## ⚠️ Gotchas

- NetBIOS over TCP/IP is often disabled on modern Windows / blocked by firewalls — may get no hits.
- Complement with [nmblookup](nmblookup.md) and `nmap --script nbstat`.
- `-r` requires root (binds privileged port 137).

---

## 🔗 Related

- [nmblookup](nmblookup.md) · [enum4linux](enum4linux.md) · [smbclient](smbclient.md) · [smbmap](smbmap.md)
