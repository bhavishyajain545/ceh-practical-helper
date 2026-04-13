# ldapsearch — query LDAP / Active Directory

> **The reliable LDAP client.** Anonymous binds, AD enumeration, naming-context discovery.

**Install check:** `ldapsearch -VV` (in `ldap-utils` package)

---

## 🎯 Cheat-flow

| Task | Command |
|---|---|
| Find the naming contexts (anonymous) | `ldapsearch -x -H ldap://DC -s base namingcontexts` |
| Anonymous dump of everything | `ldapsearch -x -H ldap://DC -b "dc=corp,dc=local"` |
| Authenticated bind | `ldapsearch -x -H ldap://DC -D "user@corp.local" -w 'Pw' -b "dc=corp,dc=local"` |
| All domain users | `ldapsearch ... -b "dc=corp,dc=local" "(objectClass=user)" sAMAccountName` |
| All groups | `ldapsearch ... "(objectClass=group)" cn` |
| Domain admins | `ldapsearch ... "(&(objectClass=user)(memberOf=CN=Domain Admins,CN=Users,DC=corp,DC=local))" sAMAccountName` |
| Computers | `ldapsearch ... "(objectClass=computer)" dNSHostName operatingSystem` |
| Kerberoastable (SPN set) | `ldapsearch ... "(&(objectClass=user)(servicePrincipalName=*))" sAMAccountName servicePrincipalName` |
| ASREP-roastable | `ldapsearch ... "(&(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=4194304))" sAMAccountName` |

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-x` | Simple auth (not SASL) |
| `-H <uri>` | `ldap://host` or `ldaps://host` |
| `-h <host> -p 389` | Alt to `-H` |
| `-D <DN>` | Bind DN (user) |
| `-w <pw>` | Bind password |
| `-W` | Prompt for password |
| `-y <file>` | Read password from file |
| `-b <base>` | Search base DN |
| `-s base|one|sub` | Search scope (default `sub`) |
| `-LLL` | Clean LDIF output (no comments) |
| `-o ldif-wrap=no` | Don't wrap long lines |
| `-E pr=500/noprompt` | Paged results (for >1000 objects in AD) |

---

## 📋 Recipes

```bash
DC=10.10.10.5
DOM="DC=corp,DC=local"

# 1. Anonymous — is it enabled?
ldapsearch -x -H ldap://$DC -s base namingcontexts

# 2. Full anonymous dump (often works on misconfigured DCs)
ldapsearch -x -H ldap://$DC -b "$DOM" -LLL > dump.ldif

# 3. Authenticated user list
ldapsearch -x -H ldap://$DC -D 'corp\jdoe' -w 'Pw!' \
  -b "$DOM" "(objectClass=user)" sAMAccountName \
  -LLL | grep sAMAccountName

# 4. Kerberoastable accounts
ldapsearch -x -H ldap://$DC -D 'jdoe@corp.local' -w 'Pw!' \
  -b "$DOM" "(&(objectClass=user)(servicePrincipalName=*))" \
  sAMAccountName servicePrincipalName -LLL

# 5. Password policy
ldapsearch -x -H ldap://$DC -D 'jdoe@corp.local' -w 'Pw!' \
  -b "$DOM" -s base "(objectClass=*)" \
  minPwdLength pwdHistoryLength maxPwdAge lockoutThreshold

# 6. Paged results (>1000 hits)
ldapsearch -x -E pr=500/noprompt ... 
```

---

## 🧠 Common AD attribute names

| Attribute | Meaning |
|---|---|
| `sAMAccountName` | login name |
| `userPrincipalName` | user@corp.local |
| `memberOf` | group DNs |
| `servicePrincipalName` | SPNs (kerberoast) |
| `userAccountControl` | flags — 4194304 = DONT_REQ_PREAUTH (ASREP) |
| `description` | often contains passwords 😄 |
| `pwdLastSet`, `lastLogon` | timestamps |
| `objectSid` | SID |

Always also grep `description` — classic leaky field.

---

## ⚠️ Gotchas

- **Anonymous bind** is rarely allowed on modern AD (disabled by default since 2003 R2) — but pops up in labs.
- AD returns **max 1000 objects** per query — use paged controls (`-E pr=500/noprompt`).
- Use `ldaps://` (636) if plaintext is blocked. Add `LDAPTLS_REQCERT=never` env var to ignore bad certs.
- For automated AD enum, [windapsearch](https://github.com/ropnop/windapsearch) / [bloodhound](https://github.com/BloodHoundAD/BloodHound) are faster — ldapsearch is the manual option.
- Filters are RFC 4515 — wrap in single quotes to protect shell.

---

## 🔗 Related

- [enum4linux](enum4linux.md) · [crackmapexec](crackmapexec.md) · [impacket](impacket.md) · [rpcclient](rpcclient.md)
