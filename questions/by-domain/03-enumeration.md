# Question Bank — Enumeration (12 questions)

> Realistic CEH-Practical-style active enumeration questions. Steps link into individual tool docs — click any command to see the full reference.

**Legend:** 🟢 easy · 🟡 medium · 🔴 hard

---

### Q1 🟢 — List SMB shares on 10.10.10.10 (anonymous)

**Category:** SMB enum | **Tools:** [smbclient](../../tools/smbclient.md), [smbmap](../../tools/smbmap.md)

**Steps:**
1. `smbclient -L //10.10.10.10/ -N` ← [why -N](../../tools/smbclient.md#anonymous-listing)
2. Or: `smbmap -H 10.10.10.10` ← [why](../../tools/smbmap.md#basic-usage)

**Answer format:** share name verbatim (e.g. `PUBLIC`) or integer count

**Gotcha:** `IPC$` and `ADMIN$` are default — exam usually wants the non-default ones.

---

### Q2 🟢 — Identify the SMB/OS version on 10.10.10.10

**Category:** SMB enum | **Tools:** [enum4linux](../../tools/enum4linux.md), [nmap](../../tools/nmap.md)

**Steps:**
1. `enum4linux -o 10.10.10.10` ← [why -o](../../tools/enum4linux.md#os-info)
2. Or: `nmap -p 445 --script smb-os-discovery 10.10.10.10`

**Answer format:** OS string verbatim (e.g. `Windows 7 Professional 7601 Service Pack 1`)

---

### Q3 🟢 — Find the NetBIOS name of 10.10.10.10

**Category:** NetBIOS | **Tools:** [nbtscan](../../tools/nbtscan.md), [nmblookup](../../tools/nmblookup.md)

**Steps:**
1. `nbtscan 10.10.10.10` ← [why](../../tools/nbtscan.md#basic-usage)
2. Or: `nmblookup -A 10.10.10.10`

**Answer format:** the NetBIOS name verbatim (e.g. `WIN-SRV01`)

**Gotcha:** the `<00>` entry is the workstation name; `<20>` is the file-server service; `<1C>` is the domain controller.

---

### Q4 🟡 — Enumerate SMB users on 10.10.10.10

**Category:** SMB enum | **Tools:** [enum4linux](../../tools/enum4linux.md), [rpcclient](../../tools/rpcclient.md)

**Steps:**
1. `enum4linux -U 10.10.10.10` ← [why -U](../../tools/enum4linux.md#user-enum)
2. Or interactive: `rpcclient -U "" -N 10.10.10.10` then `enumdomusers` ← [why](../../tools/rpcclient.md#enumdomusers)

**Answer format:** username verbatim or integer count

**Gotcha:** if anonymous binds are blocked, try `rpcclient -U "guest%" 10.10.10.10`.

---

### Q5 🟡 — Find the share containing `flag.txt` on 10.10.10.10

**Category:** SMB enum | **Tools:** [smbmap](../../tools/smbmap.md), [smbclient](../../tools/smbclient.md)

**Steps:**
1. List shares with read access: `smbmap -H 10.10.10.10 -u anonymous` ← [why](../../tools/smbmap.md#permissions)
2. Recursive file listing: `smbmap -H 10.10.10.10 -u anonymous -R` ← [why -R](../../tools/smbmap.md#recursive)
3. Grep: `smbmap -H 10.10.10.10 -u anonymous -R | grep -i flag.txt`
4. Pull it: `smbclient //10.10.10.10/<share> -N -c "get flag.txt"`

**Answer format:** share name, or the file contents

**Gotcha:** `-R` can be slow on deep shares — use `--depth 3` to limit.

---

### Q6 🟡 — Identify the SNMP community string on 10.10.10.20

**Category:** SNMP enum | **Tools:** [snmpwalk](../../tools/snmpwalk.md), [onesixtyone](../../tools/onesixtyone.md)

**Steps:**
1. Confirm 161/udp open: `sudo nmap -sU -p 161 10.10.10.20`
2. Fast brute: `onesixtyone -c /usr/share/wordlists/snmp.txt 10.10.10.20` ← [why](../../tools/onesixtyone.md#basic-usage)
3. Test manually: `snmpwalk -v2c -c public 10.10.10.20 system` ← [why -c](../../tools/snmpwalk.md#community-string)

**Answer format:** the community string (usually `public` or `private`)

---

### Q7 🟡 — Read the system description (sysDescr) of 10.10.10.20 via SNMP

**Category:** SNMP enum | **Tools:** [snmpwalk](../../tools/snmpwalk.md)

**Steps:**
1. `snmpwalk -v2c -c public 10.10.10.20 1.3.6.1.2.1.1.1.0` ← [why this OID](../../tools/snmpwalk.md#common-oids)
2. Or use the MIB name: `snmpwalk -v2c -c public 10.10.10.20 sysDescr`

**Answer format:** the full sysDescr string verbatim (e.g. `Hardware: x86 ... Windows Version 6.1`)

**Gotcha:** memorize the big 4 OIDs: `1.3.6.1.2.1.1.1.0` (sysDescr), `1.3.6.1.2.1.25.4.2.1.2` (processes), `1.3.6.1.2.1.25.6.3.1.2` (software), `1.3.6.1.4.1.77.1.2.25` (users).

---

### Q8 🟡 — List running processes on 10.10.10.20 via SNMP

**Category:** SNMP enum | **Tools:** [snmpwalk](../../tools/snmpwalk.md)

**Steps:**
1. `snmpwalk -v2c -c public 10.10.10.20 1.3.6.1.2.1.25.4.2.1.2` ← [why](../../tools/snmpwalk.md#common-oids)
2. Or: `snmpwalk -v2c -c public 10.10.10.20 hrSWRunName`

**Answer format:** process name verbatim or integer count

---

### Q9 🟡 — Find the LDAP base DN on 10.10.10.30

**Category:** LDAP enum | **Tools:** [ldapsearch](../../tools/ldapsearch.md)

**Steps:**
1. Anonymous root DSE: `ldapsearch -x -h 10.10.10.30 -s base -b "" namingContexts` ← [why](../../tools/ldapsearch.md#root-dse)
2. Read `namingContexts:` in output.

**Answer format:** DN string (e.g. `DC=example,DC=local`)

**Gotcha:** Windows AD LDAP usually lives on 389 (LDAP) and 636 (LDAPS) — try both.

---

### Q10 🟡 — Enumerate users from LDAP on 10.10.10.30

**Category:** LDAP enum | **Tools:** [ldapsearch](../../tools/ldapsearch.md)

**Steps:**
1. Anonymous user list:
   ```bash
   ldapsearch -x -h 10.10.10.30 -b "DC=example,DC=local" "(objectClass=user)" sAMAccountName
   ```
   ← [why -b](../../tools/ldapsearch.md#search-base)
2. Grep usernames: `... | grep sAMAccountName`

**Answer format:** username verbatim or integer count

**Gotcha:** if anonymous bind is disabled, try `-D "CN=guest,CN=Users,DC=example,DC=local" -w ''`.

---

### Q11 🔴 — List NFS exports on 10.10.10.40

**Category:** NFS enum | **Tools:** [showmount](../../tools/showmount.md)

**Steps:**
1. `showmount -e 10.10.10.40` ← [why -e](../../tools/showmount.md#list-exports)
2. Mount read-only:
   ```bash
   sudo mkdir /mnt/nfs
   sudo mount -t nfs 10.10.10.40:/export /mnt/nfs -o ro
   ```
3. `ls -la /mnt/nfs`

**Answer format:** export path (e.g. `/home`) or a filename inside

**Gotcha:** `no_root_squash` + writable export = trivial priv-esc path; note this on exam.

---

### Q12 🔴 — SMTP user enumeration on 10.10.10.50

**Category:** SMTP enum | **Tools:** [smtp-user-enum](../../tools/smtp-user-enum.md), `nc`

**Steps:**
1. Confirm 25 open: `nmap -p 25 10.10.10.50`
2. Brute:
   ```bash
   smtp-user-enum -M VRFY -U /usr/share/wordlists/users.txt -t 10.10.10.50
   ```
   ← [why VRFY](../../tools/smtp-user-enum.md#modes)
3. Try EXPN and RCPT if VRFY is disabled.
4. Manual probe:
   ```bash
   nc 10.10.10.50 25
   VRFY root
   ```

**Answer format:** valid username verbatim or integer count

**Gotcha:** response codes: `250` = user exists, `252` = can't verify (still treat as exists), `550` = no such user.

---

## Quick links

- [enum4linux reference](../../tools/enum4linux.md)
- [smbclient reference](../../tools/smbclient.md)
- [smbmap reference](../../tools/smbmap.md)
- [snmpwalk reference](../../tools/snmpwalk.md)
- [ldapsearch reference](../../tools/ldapsearch.md)
- [rpcclient reference](../../tools/rpcclient.md)
- [Enumeration playbook (decision tree)](../../playbooks/enumeration-playbook.md)
- [03-enumeration README](../../03-enumeration/README.md)
- [Previous: 02-scanning](02-scanning.md) · [Next: 04-vuln-analysis](04-vuln-analysis.md)
