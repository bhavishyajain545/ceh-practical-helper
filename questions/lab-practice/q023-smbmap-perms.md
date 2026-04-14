# Q023 — smbmap Permission Mapping

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟢 Easy |
| **Tools** | `smbmap` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Run `smbmap -H 192.168.52.129` and report which share has **READ, WRITE** permissions.

---

## 🎯 Flag Format

```
rw_share=<name>
```

Example: `rw_share=tmp`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`smbmap -H <ip>` enumerates shares + ACLs without creds.
</details>

<details>
<summary>Hint 2</summary>

READ, WRITE → world-writable share.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
smbmap -H 192.168.52.129
```

`tmp` share is READ, WRITE.

**Answer:** `rw_share=tmp`

📖 Ref: [tools/enum4linux.md](../../tools/enum4linux.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q023:
1. Verify Metasploitable2 (192.168.52.129) up; samba running (sudo service samba status).
2. On Parrot confirm smbmap is installed: which smbmap (install via sudo apt install smbmap if missing).
3. From Parrot: nmap -p 445 192.168.52.129 — open.

Report back: "Lab ready for Q023 — smbmap installed on Parrot, port 445 open on 192.168.52.129".
```
