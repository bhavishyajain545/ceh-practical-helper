# Q022 — smbclient Anonymous Listing

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `smbclient` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Use `smbclient -L //192.168.52.129 -N` to list shares anonymously. How many disk shares are listed?

---

## 🎯 Flag Format

```
disk_shares=<n>
```

Example: `disk_shares=4`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-L` list shares; `-N` no password.
</details>

<details>
<summary>Hint 2</summary>

Type column = `Disk` rows.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
smbclient -L //192.168.52.129 -N
```

Shows print$, tmp, opt, IPC$ — disk count varies (4).

**Answer:** `disk_shares=4`

📖 Ref: [tools/enum4linux.md](../../tools/enum4linux.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q022:
1. Verify Metasploitable2 (192.168.52.129) reachable.
2. Confirm samba/nmbd running: ssh msfadmin@192.168.52.129 → sudo service samba status; sudo service nmbd status (start if down).
3. From Parrot: nmap -p 139,445 192.168.52.129 — both open.
4. No credentials needed — anon listing allowed by default.

Report back: "Lab ready for Q022 — SMB null listing works on 192.168.52.129".
```
