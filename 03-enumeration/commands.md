# 03 Enumeration — copy-paste commands

> Replace `<IP>` with the target. `<COMMUNITY>` = SNMP string (try `public` first).

## SMB / NetBIOS (139, 445)

```bash
# The one-shot — runs every enum4linux module
enum4linux-ng -A <IP> | tee enum4linux.txt

# Classic enum4linux (on exam VMs that still have it)
enum4linux -a <IP>

# List shares via null session
smbclient -L //<IP>/ -N

# Mount / browse a share
smbclient //<IP>/<SHARE> -N
# inside: ls, get file, recurse ON, prompt OFF, mget *

# Share permissions (very fast)
smbmap -H <IP>                          # anon
smbmap -H <IP> -u <USER> -p <PASS>      # authed
smbmap -H <IP> -R <SHARE>               # recurse list

# NetBIOS names on a LAN
nbtscan <RANGE>

# RPC null session — users, domains, SIDs
rpcclient -U "" -N <IP>
# inside:
#   srvinfo
#   enumdomusers
#   enumdomgroups
#   querydominfo
#   lsaquery           # domain SID
#   lookupnames <USER>
```

→ See [enum4linux.md](../tools/enum4linux.md) • [smbclient.md](../tools/smbclient.md) • [smbmap.md](../tools/smbmap.md) • [rpcclient.md](../tools/rpcclient.md)

## SNMP (161/udp)

```bash
# 1. Find the community string (no guessing)
onesixtyone -c /usr/share/doc/onesixtyone/dict.txt <IP>

# 2. Walk the whole tree
snmpwalk -v2c -c <COMMUNITY> <IP>

# 3. Targeted OIDs
snmpwalk -v2c -c <COMMUNITY> <IP> 1.3.6.1.2.1.25.4.2.1.2   # processes
snmpwalk -v2c -c <COMMUNITY> <IP> 1.3.6.1.4.1.77.1.2.25    # Windows users
snmpwalk -v2c -c <COMMUNITY> <IP> 1.3.6.1.2.1.6.13.1.3     # open TCP ports

# 4. snmp-check (human-readable summary)
snmp-check <IP> -c <COMMUNITY>
```

→ See [snmpwalk.md](../tools/snmpwalk.md) • [onesixtyone.md](../tools/onesixtyone.md)

## LDAP (389, 636)

```bash
# Anonymous — find the base DN
ldapsearch -x -H ldap://<IP> -s base namingcontexts

# Dump everything under a base DN
ldapsearch -x -H ldap://<IP> -b "dc=example,dc=com"

# Only users
ldapsearch -x -H ldap://<IP> -b "dc=example,dc=com" "(objectClass=person)"

# With creds
ldapsearch -x -H ldap://<IP> -D "cn=admin,dc=example,dc=com" -w <PASS> -b "dc=example,dc=com"
```

→ See [ldapsearch.md](../tools/ldapsearch.md)

## NFS (2049)

```bash
# List exports
showmount -e <IP>

# Mount
sudo mkdir -p /mnt/nfs
sudo mount -t nfs <IP>:/<EXPORT> /mnt/nfs -o nolock
ls -la /mnt/nfs
```

→ See [showmount.md](../tools/showmount.md)

## SMTP (25)

```bash
# VRFY method (old servers)
smtp-user-enum -M VRFY -U /usr/share/wordlists/names.txt -t <IP>

# Manual
nc <IP> 25
HELO test
VRFY root
VRFY admin
```

## DNS (53) — quick enum

```bash
dig axfr @<IP> <DOMAIN>
dnsrecon -d <DOMAIN> -n <IP> -t axfr
```

→ Full DNS recon: [../01-recon-footprinting/commands.md](../01-recon-footprinting/commands.md)

## Finger (79)

```bash
finger @<IP>
finger root@<IP>
```

## Save everything

```bash
mkdir -p enum && cd enum
enum4linux-ng -A <IP> > smb.txt
snmpwalk -v2c -c public <IP> > snmp.txt
showmount -e <IP> > nfs.txt
```
