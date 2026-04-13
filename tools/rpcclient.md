# rpcclient — Windows MS-RPC client

> **Anonymous null-session enumeration for legacy / misconfigured Windows & Samba.** Lists users, groups, shares, domain SID, password policy.

**Install check:** `rpcclient -V` (samba-common-bin)

---

## 🎯 Cheat-flow

```bash
rpcclient -U "" -N 10.10.10.5              # anonymous (null session)
rpcclient -U "guest%" 10.10.10.5           # guest / blank
rpcclient -U 'corp\jdoe%Pass!' 10.10.10.5  # authenticated
```

Inside the interactive prompt:

```text
rpcclient $> srvinfo
rpcclient $> enumdomusers
rpcclient $> enumdomgroups
rpcclient $> enumalsgroups domain
rpcclient $> querydominfo
rpcclient $> getdompwinfo
rpcclient $> netshareenum
rpcclient $> netshareenumall
rpcclient $> lsaquery
rpcclient $> lookupnames administrator
rpcclient $> lookupsids S-1-5-21-...-500
rpcclient $> queryuser 0x1f4               # RID 500 = Administrator
```

---

## 🔑 Key commands

| Command | Purpose |
|---|---|
| `srvinfo` | Server info (OS version, type) |
| `enumdomusers` | List all domain users + RIDs |
| `enumdomgroups` | Domain groups |
| `enumalsgroups {builtin|domain}` | Alias groups |
| `querydominfo` | Domain name, SID, user count |
| `getdompwinfo` | **Password policy** (min len, complexity) |
| `queryuser <rid>` | Per-user detail (last logon, bad pw count, description) |
| `queryusergroups <rid>` | Groups for user |
| `lookupnames <name>` | Name → SID |
| `lookupsids <sid>` | SID → name (RID cycling) |
| `netshareenumall` | Shares list |
| `netsharegetinfo <name>` | ACL on a share |
| `enumprivs` | LSA privileges |
| `lsaquery` | Domain / SID |

---

## 📋 RID cycling (enum when enumdomusers is blocked)

```bash
for i in $(seq 500 1100); do
  rpcclient -U "" -N -c "lookupsids S-1-5-21-DOMAIN-$i" 10.10.10.5
done
```

`S-1-5-21-<dom>-500` = local/domain Administrator. RID 501 = Guest. 1000+ = created accounts.

---

## 📋 Non-interactive one-liners

```bash
rpcclient -U "" -N -c "enumdomusers" 10.10.10.5
rpcclient -U "" -N -c "getdompwinfo" 10.10.10.5
rpcclient -U "" -N -c "netshareenumall" 10.10.10.5
```

---

## ⚠️ Gotchas

- Null sessions are mostly **blocked on Windows Vista+**. They still work on old Samba, misconfigured Server 2003/2008, and some lab boxes.
- Authenticated rpcclient with any low-priv user often gives you the same commands — pair with [crackmapexec](crackmapexec.md) for faster bulk enum.
- Errors `NT_STATUS_ACCESS_DENIED` are normal — just try another command.
- `-N` = no password (prompt suppressed).

---

## 🔗 Related

- [enum4linux](enum4linux.md) · [smbclient](smbclient.md) · [crackmapexec](crackmapexec.md) · [ldapsearch](ldapsearch.md) · [impacket](impacket.md)
