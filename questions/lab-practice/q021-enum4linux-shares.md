# Q021 — Enum4linux SMB Shares

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 03 — Enumeration |
| **Difficulty** | 🟡 Medium |
| **Tools** | `enum4linux` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Run `enum4linux -a` on Metasploitable and identify the share named for **temporary files** and the share for **printer drivers**.

---

## 🎯 Flag Format

```
tmp_share=<name>; printer_share=<name>
```

Example: `tmp_share=tmp; printer_share=print$`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`enum4linux -a <ip>` runs everything.
</details>

<details>
<summary>Hint 2</summary>

Look under `Share Enumeration`.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
enum4linux -a 192.168.52.129
```

Shares: print$, tmp, opt, IPC$, ADMIN$.

**Answer:** `tmp_share=tmp; printer_share=print$`

📖 Ref: [tools/enum4linux.md](../../tools/enum4linux.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q021:
1. Verify Metasploitable2 (192.168.52.129) up and reachable: ping 192.168.52.129 from Parrot.
2. Confirm Samba running on target: ssh msfadmin@192.168.52.129 → sudo service samba status (start if not running).
3. From Parrot sanity-check: nmap -p 139,445 192.168.52.129 — both must be open.
4. No auth config change needed; Metasploitable2 ships with null-session enabled.

Report back: "Lab ready for Q021 — SMB 139/445 open on 192.168.52.129, null session allowed".
```
