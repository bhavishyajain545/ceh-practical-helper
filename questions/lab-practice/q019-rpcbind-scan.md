# Q019 — RPC Service Enumeration

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Use nmap `-sR` (or `--script rpcinfo`) on Metasploitable port 111. Report the **NFS version** registered.

---

## 🎯 Flag Format

```
nfs_version=<n>
```

Example: `nfs_version=2`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`nmap -sV --script=rpcinfo -p 111 <ip>`
</details>

<details>
<summary>Hint 2</summary>

Look for `nfs <version>` lines.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
nmap -sV --script=rpcinfo -p 111 192.168.52.129
```

Reports NFS v2/3/4 — Metasploitable typically v2.

**Answer:** `nfs_version=2`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q019:
1. Verify Metasploitable2 (192.168.52.129) is reachable: ping -c 2 192.168.52.129.
2. portmap/rpcbind must be listening on TCP 111: nc -vz 192.168.52.129 111 → "succeeded".
3. nfs-kernel-server must be registered in portmap so rpcinfo lists it:
   - From Parrot quick test: rpcinfo -p 192.168.52.129 | grep nfs → should show nfs 2, 3 (and portmapper, mountd, nlockmgr).
   - If nfs missing: ssh msfadmin@192.168.52.129 → sudo service portmap start; sudo service nfs-kernel-server start.
4. rpcinfo NSE script ships with nmap: ls /usr/share/nmap/scripts/rpcinfo.nse.
5. No root required for nmap -sV --script=rpcinfo -p 111 (TCP connect).

Report back: "Lab ready for Q019 — rpcbind up on 192.168.52.129:111 and nfs registered".
```
