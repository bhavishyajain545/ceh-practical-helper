# 03 Enumeration — full walkthroughs

End-to-end multi-step examples. Assumes scanning is already done.

---

## Walkthrough 1: "List the SMB shares on 10.10.10.3"

**Type:** SMB enum | **Difficulty:** easy | **Time:** 1–2 min

1. Confirm SMB is open (quick sanity check):
   ```bash
   nmap -p 139,445 10.10.10.3
   ```
2. Try null-session share list ([why null](../tools/smbclient.md#null-sessions)):
   ```bash
   smbclient -L //10.10.10.3/ -N
   ```
3. Example output:
   ```
       Sharename       Type      Comment
       ---------       ----      -------
       print$          Disk      Printer Drivers
       tmp             Disk      Temporary files
       opt             Disk
       IPC$            IPC       IPC Service
       ADMIN$          Disk      IT Admin
   ```
4. Cross-check with smbmap to see which are readable:
   ```bash
   smbmap -H 10.10.10.3
   ```
5. **Answer:** list the share names (exclude `IPC$` and `print$` unless asked).

**Gotcha:** if you get `NT_STATUS_ACCESS_DENIED`, try `-U "guest"%""` or `-U ""%""` — some servers reject a pure null but accept empty credentials.

---

## Walkthrough 2: "Find the SNMP community string on 10.10.10.8 and list the running processes"

**Type:** SNMP enum | **Difficulty:** medium | **Time:** 3–5 min

1. Confirm 161/udp is open:
   ```bash
   sudo nmap -sU -p 161 10.10.10.8
   ```
2. Brute-force the community string ([why onesixtyone](../tools/onesixtyone.md)):
   ```bash
   onesixtyone -c /usr/share/doc/onesixtyone/dict.txt 10.10.10.8
   ```
   Output:
   ```
   10.10.10.8 [public] Linux target 5.4.0
   10.10.10.8 [private] Linux target 5.4.0
   ```
3. Walk the process OID:
   ```bash
   snmpwalk -v2c -c public 10.10.10.8 1.3.6.1.2.1.25.4.2.1.2
   ```
4. Example output:
   ```
   HOST-RESOURCES-MIB::hrSWRunName.1 = STRING: "systemd"
   HOST-RESOURCES-MIB::hrSWRunName.2 = STRING: "kthreadd"
   ...
   ```
5. **Answer:** `public` (or whatever onesixtyone found), plus the process name list.

**Gotcha:**
- UDP scans are slow — don't forget `-sU` needs `sudo`
- SNMPv3 won't answer to v2c — fall back to `snmpwalk -v3 ...` with user/key
- Some CEH questions want the **count** of processes — use `| wc -l`

---

## Walkthrough 3: "Find a readable NFS export on 10.10.10.11 and read /flag.txt"

**Type:** NFS enum | **Difficulty:** medium | **Time:** 3 min

1. Confirm 2049 is open:
   ```bash
   nmap -p 111,2049 10.10.10.11
   ```
2. List exports:
   ```bash
   showmount -e 10.10.10.11
   ```
   Output:
   ```
   Export list for 10.10.10.11:
   /home/shared *
   /backup      10.10.10.0/24
   ```
3. Mount the first one:
   ```bash
   sudo mkdir -p /mnt/nfs
   sudo mount -t nfs 10.10.10.11:/home/shared /mnt/nfs -o nolock
   ls -la /mnt/nfs
   ```
4. Read the flag:
   ```bash
   cat /mnt/nfs/flag.txt
   ```
5. **Answer:** the file contents, verbatim.

**Gotcha:**
- `Permission denied` even with wildcard export → the file is root-owned + `no_root_squash` is off. Mount it, then `sudo -u "#<UID>" cat ...` matching the file owner.
- Always `sudo umount /mnt/nfs` before moving on — leaving mounts up will hang future scans.

---

## Walkthrough 4: "Enumerate domain users on the Windows host 10.10.10.14"

**Type:** SMB / RPC enum | **Difficulty:** medium | **Time:** 3 min

1. Kick off enum4linux-ng (one shot):
   ```bash
   enum4linux-ng -A 10.10.10.14 | tee enum.txt
   ```
2. While it runs, open an RPC null session in another pane:
   ```bash
   rpcclient -U "" -N 10.10.10.14
   ```
3. From the `rpcclient>` prompt:
   ```
   enumdomusers
   querydominfo
   lsaquery
   ```
4. Example output:
   ```
   user:[Administrator] rid:[0x1f4]
   user:[Guest]         rid:[0x1f5]
   user:[krbtgt]        rid:[0x1f6]
   user:[jsmith]        rid:[0x450]
   ```
5. **Answer:** the user list (or count), matching what the question asks.

**Gotcha:**
- Modern Windows (2012+) blocks null RPC by default — you'll get `NT_STATUS_ACCESS_DENIED`. Fall back to a valid low-priv account.
- Don't miss `enum4linux-ng`'s "users via RID cycling" section — it often reveals users that `enumdomusers` hides.
- `krbtgt` in the list = domain controller — note it for later AD attacks. See [05-system-hacking](../05-system-hacking/README.md) *(coming)*.
