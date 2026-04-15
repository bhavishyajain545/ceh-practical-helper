# bloodhound — Active Directory attack-path discovery

> **The AD graph tool.** Collects every user, group, ACL, session, and trust in a domain, loads them into a Neo4j graph database, and lets you run Cypher queries to find the shortest attack path from any pwned user → Domain Admin. Essential for CEH v12/v13 AD scenarios (scenario-13 and scenario-24 in this repo).

**Install check (Parrot):** `bloodhound --version`
**Install if missing:** `sudo apt install bloodhound bloodhound.py` *(the `.py` package = Linux collector)*

---

## 🏗 Components

| Piece | Purpose | Where it runs |
|---|---|---|
| **Neo4j** | Graph database backend | Parrot (your box) |
| **BloodHound UI** | Electron/JS frontend that renders the graph | Parrot |
| **SharpHound.exe** | Data collector for Windows (AD-joined host) | Target Windows box |
| **bloodhound-python** | Data collector for Linux (pure-Python, uses LDAP + SMB) | Parrot |

---

## 🎯 Cheat-flow: "Given domain creds, find the shortest path to DA"

| Step | Command |
|---|---|
| 1. Start Neo4j | `sudo neo4j console` (keep this tab open) |
| 2. First-time password | browse `http://localhost:7474` → login `neo4j:neo4j` → set new pw |
| 3. Launch BloodHound UI | `bloodhound &` (login with Neo4j creds) |
| 4. Collect from **Linux** | `bloodhound-python -u user -p 'pass' -ns <DC_IP> -d corp.local -c All --zip` |
| 4b. Or collect from **Windows** | `SharpHound.exe -c All` (drops a zip) |
| 5. Import into BloodHound | drag the `.zip` into the UI window |
| 6. Query | Analysis tab → **"Find Shortest Paths to Domain Admins"** |

---

## 🔑 bloodhound-python flags

| Flag | Meaning |
|---|---|
| `-u <user>` | Domain username |
| `-p <pass>` | Password |
| `-H <LM:NT>` or `-H <NT>` | Pass-the-hash |
| `-k` | Use Kerberos (needs `KRB5CCNAME`) |
| `-ns <DC_IP>` | **Nameserver / DC** (mandatory for LDAP + DNS) |
| `-d <domain>` | FQDN (e.g. `corp.local`) |
| `-dc <fqdn>` | Explicit DC hostname |
| `-c <method>` | Collection methods (see below) |
| `--zip` | Bundle JSON outputs into one zip for import |
| `-w <threads>` | Worker threads |
| `-gc <fqdn>` | Global Catalog (cross-domain) |

### Collection methods (`-c`)

| Method | Gathers |
|---|---|
| `Default` | Groups, LocalAdmin, Sessions, Trusts |
| `Group` | Group memberships only |
| `LocalAdmin` | Who is local admin where |
| `RDP` | Who can RDP to where |
| `DCOM` | Who has DCOM rights |
| `PSRemote` | WinRM rights |
| `Session` | Active user sessions |
| `Trusts` | Domain trusts |
| `ACL` | Object ACLs (often the money data) |
| `Container` | OU / container structure |
| `ObjectProps` | User/computer properties |
| `LoggedOn` | Logged-on users (needs admin on target) |
| **`All`** | **Everything except LoggedOn** |
| `DCOnly` | LDAP-only, no target touching (stealth) |

---

## 🔑 SharpHound.exe flags (Windows-side)

| Flag | Meaning |
|---|---|
| `-c <method>` | Same methods as above; use `All` |
| `--CollectionMethods All` | Long form |
| `-d <domain>` | Domain override |
| `--Stealth` | Avoid LoggedOn + heavy collection |
| `--ExcludeDCs` | Don't touch DCs (safer) |
| `--LDAPUsername` / `--LDAPPassword` | Alt creds |
| `--OutputDirectory <dir>` | Where to drop zip |
| `--ZipFileName name.zip` | Custom zip name |
| `--Loop` | Periodic re-collection (long engagements) |

```cmd
SharpHound.exe -c All --ZipFileName loot.zip
SharpHound.exe -c All --Stealth --ExcludeDCs
```

---

## 📋 Command recipes

```bash
# 1. Start everything (two terminals)
sudo neo4j console                                  # terminal 1
bloodhound &                                        # terminal 2

# 2. Collect from Parrot using valid domain creds
bloodhound-python -u alice -p 'Welcome1!' \
  -ns 192.168.52.10 -d corp.local -c All --zip

# 3. Collect via pass-the-hash
bloodhound-python -u alice -H :31d6cfe0d16ae931b73c59d7e0c089c0 \
  -ns 192.168.52.10 -d corp.local -c All --zip

# 4. Kerberos (TGT already in ccache)
export KRB5CCNAME=$(pwd)/alice.ccache
bloodhound-python -k -u alice -ns 192.168.52.10 -d corp.local -c All --zip

# 5. After import, mark your starting user as "Owned"
#    Right-click in UI → "Mark User as Owned"
#    Then run: "Shortest Paths from Owned Principals to Domain Admins"
```

---

## 🧠 Pre-canned queries (the ones you'll actually use)

In the UI → **Analysis** tab → left sidebar:

| Query | Why |
|---|---|
| **Find all Domain Admins** | Baseline — who is DA today |
| **Find Shortest Paths to Domain Admins** | The exam answer for "how do I become DA" |
| **Find Shortest Paths from Owned Principals** | Path from your foothold |
| **Find AS-REP Roastable Users** | DONT_REQ_PREAUTH set → feed to `GetNPUsers.py` |
| **Find Kerberoastable Users** | SPN set → feed to `GetUserSPNs.py` |
| **Find Principals with DCSync Rights** | Who can pull krbtgt |
| **Find Computers where Domain Users are Local Admin** | Lateral movement candidates |
| **Map Domain Trusts** | Cross-forest paths |
| **Shortest Paths to Unconstrained Delegation** | Abuse for DA |
| **Shortest Paths to High Value Targets** | DAs + Enterprise Admins + DCs |
| **Find Principals with Foreign Domain Group Memberships** | Cross-domain pivots |

---

## 🧪 Custom Cypher (when pre-canned isn't enough)

BloodHound uses the Neo4j Cypher query language. Open **Raw Query** box at bottom.

```cypher
// 1. List all users in Domain Admins
MATCH (u:User)-[:MemberOf*1..]->(g:Group {name:"DOMAIN ADMINS@CORP.LOCAL"})
RETURN u.name

// 2. Users with passwords never expiring
MATCH (u:User {pwdneverexpires:true}) RETURN u.name

// 3. Users with SPN (Kerberoastable)
MATCH (u:User) WHERE u.hasspn=true RETURN u.name, u.serviceprincipalnames

// 4. Users without Kerberos preauth (AS-REP roastable)
MATCH (u:User {dontreqpreauth:true}) RETURN u.name

// 5. Shortest path from Alice to DAs
MATCH p=shortestPath((a:User {name:"ALICE@CORP.LOCAL"})-[*1..]->
      (g:Group {name:"DOMAIN ADMINS@CORP.LOCAL"}))
RETURN p

// 6. Computers where Domain Users are local admin
MATCH (g:Group {name:"DOMAIN USERS@CORP.LOCAL"})-[:AdminTo]->(c:Computer)
RETURN c.name

// 7. Find sessions of DAs (where they're logged in → PtH target)
MATCH (u:User)-[:MemberOf*1..]->(g:Group {name:"DOMAIN ADMINS@CORP.LOCAL"})
MATCH (u)-[:HasSession]->(c:Computer)
RETURN u.name, c.name
```

---

## 🎯 CEH / scenario patterns

**"Given domain creds, list kerberoastable users":**
```bash
bloodhound-python -u user -p pass -ns <DC> -d corp.local -c All --zip
# Import zip → Analysis → "Find Kerberoastable Users"
# Or pure Cypher:
MATCH (u:User) WHERE u.hasspn=true RETURN u.name, u.serviceprincipalnames
```

**"Find the shortest path from user BOB to Domain Admins":**
1. Import the SharpHound zip
2. Right-click BOB → Mark User as Owned
3. Run query "Shortest Paths from Owned Principals to Domain Admins"

**Lab note (this repo):** The Win7 lab VM is a **workgroup**, not domain-joined, so BloodHound has no data to collect locally. Practice BloodHound against HTB/THM AD boxes (Forest, Sauna, Resolute) or a quick `samba-ad-dc` container. The UI + query skills are exam-critical even without a lab DC.

---

## ⚠️ Gotchas

- **Neo4j default creds** `neo4j:neo4j` must be changed on first login before BloodHound connects. The UI refuses default pw.
- **Two BloodHound versions:** legacy **BloodHound 4.x** (Electron UI, used here) and new **BloodHound CE / Enterprise** (web-based). Collectors overlap; JSON format differs slightly. Parrot ships 4.x.
- **`bloodhound-python` needs DNS** — use `-ns <DC_IP>` or add DC to `/etc/resolv.conf`. Also `/etc/hosts` with `DC_IP dc.corp.local` helps.
- **Kerberos time skew** > 5 min breaks `-k`. `sudo ntpdate <DC_IP>`.
- **`-c All` includes LocalAdmin / Session collection** which touches SMB on each host — noisy. Use `-c DCOnly` for stealth.
- **Big domains = huge graph** — render slows. Use filters / smaller queries.
- **SharpHound on modern Windows** triggers Defender immediately. Use AMSI bypass or obfuscated build for lab; on the exam usually defenders are off.
- **Relationship edges missing?** You probably ran `-c Default` not `-c All`. Re-collect.
- **Multi-domain forests:** use `-gc <GC_FQDN>` and run once per domain; merge zips.
- **Zip import hangs** → UI is processing; check Neo4j memory (`NEO4J_dbms_memory_heap_max__size=2G`).

---

## 🔗 Related

- [impacket](impacket.md) — `GetUserSPNs.py` / `GetNPUsers.py` feed from BloodHound findings
- [crackmapexec](crackmapexec.md) — `-M bloodhound` module runs SharpHound-equivalent collection
- [kerbrute](kerbrute.md) — user-enum to populate BloodHound when you have no creds
- [mimikatz](mimikatz.md) — post-DCSync once BloodHound shows the path
- [evil-winrm](evil-winrm.md) — shell once you've picked a lateral host
- [05-system-hacking domain README](../05-system-hacking/README.md)
