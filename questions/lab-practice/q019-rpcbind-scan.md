# Q019 — RPC Service Enumeration

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap`, `hping3`, `masscan`, `nc` |
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
1. Metasploitable nfs-kernel-server running.

Report back: "Lab ready for Q019".
```
