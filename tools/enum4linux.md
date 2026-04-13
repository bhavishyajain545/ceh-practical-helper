# enum4linux — SMB / NetBIOS enumeration

> **The SMB swiss-army knife.** Wraps `smbclient`, `rpcclient`, `net`, `nmblookup` into one command. First tool to run against port 139/445.

**Install check (Parrot — already installed):** `enum4linux -h`
**NG variant:** `enum4linux-ng -h` (Python rewrite, cleaner JSON output, actively maintained)

---

## 🎯 Cheat-flow: "What do I run?"

| You need to find... | Run this | Why |
|---|---|---|
| **Everything** (first move) | `enum4linux -a <IP>` | Full dump: users, shares, OS, policy |
| Users only | `enum4linux -U <IP>` | RID cycling + SAMR |
| Shares only | `enum4linux -S <IP>` | List shares with access |
| Password policy | `enum4linux -P <IP>` | Lockout / complexity |
| Groups | `enum4linux -G <IP>` | Local + domain groups |
| OS / workgroup / domain | `enum4linux -o <IP>` | OS info |
| RID cycle (find hidden users) | `enum4linux -r <IP>` | Bruteforce RIDs |
| Use creds | `enum4linux -u user -p pass -a <IP>` | Authenticated enum |
| Cleaner output / JSON | `enum4linux-ng -A <IP>` | NG version |

---

## 🔑 Flags you must know cold

| Flag | Meaning |
|---|---|
| `-a` | **Do all** simple enumeration (-U -S -G -P -o -n -i) |
| `-U` | Get userlist |
| `-S` | Get sharelist |
| `-P` | Get password policy |
| `-G` | Get group and member list |
| `-o` | Get OS information |
| `-n` | Get nmblookup (NetBIOS names) |
| `-i` | Get printer info |
| `-r` | RID cycle (even without null session) |
| `-R <ranges>` | RID ranges, default `500-550,1000-1050` |
| `-u <user>` | Username for authenticated enum |
| `-p <pass>` | Password |
| `-w <workgroup>` | Specify workgroup/domain |
| `-v` | Verbose |

### enum4linux-ng extras
| Flag | Meaning |
|---|---|
| `-A` | All enum (like -a) |
| `-As` | All + share brute-force |
| `-oJ file` | Output JSON |
| `-oY file` | Output YAML |
| `-d` | Detailed |

---

## 📋 Command recipes (copy-paste)

```bash
# 1. The "always run first" — full null-session enum
enum4linux -a <IP> | tee enum4linux.txt

# 2. NG version (preferred if installed)
enum4linux-ng -A <IP> -oY enum.yaml

# 3. Authenticated enum (after you find creds)
enum4linux -u 'guest' -p '' -a <IP>
enum4linux -u 'admin' -p 'Password123' -a <IP>

# 4. RID cycle for hidden users (extended range)
enum4linux -r -R 500-2000 <IP>

# 5. Just users (fast)
enum4linux -U <IP> | grep 'user:'

# 6. Just shares
enum4linux -S <IP>

# 7. Password policy (needed for bruteforce planning)
enum4linux -P <IP>
```

---

## ⚠️ Gotchas

- **Null sessions** (anonymous) are disabled on modern Windows. If `-a` returns nothing, it's probably Win10/2016+. Try `-u 'guest' -p ''` or any creds.
- **SMB1 vs SMB2** — enum4linux uses legacy tools that may fail on SMB2-only hosts. If you see `NT_STATUS_CONNECTION_DISCONNECTED`, switch to [smbclient](./smbclient.md) or [crackmapexec](./crackmapexec.md).
- **Output is noisy** — always pipe to `tee` or a file. Grep for `user:`, `Sharename`, `Domain Sid`.
- **Slow** on large domains — RID cycling to 10000 can take a while.
- **enum4linux-ng is better** if available — JSON output, handles SMB2, clearer errors.
- **Domain SID** is the prize — save it for `lookupsid.py` and RID brute.

---

## 🔗 Related

- [smbclient](./smbclient.md) — manual share interaction
- [smbmap](./smbmap.md) — share permission mapping
- [crackmapexec](./crackmapexec.md) — bulk SMB auth / exec
- [impacket](./impacket.md) — `lookupsid.py`, `samrdump.py`
- [nmap](./nmap.md) — `--script "smb-enum-*"`
