# Q117 — XOR Single-Byte Decode (Concept)

| Field | Value |
|---|---|
| **Target** | (local files on Parrot) |
| **Domain** | 18 — Cryptography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `openssl`, `steghide`, `binwalk`, `exiftool`, `hashid` |
| **Time budget** | 10–15 min |

---

## 📝 Question

A file is XORed with single byte `0x42`. Provide bash one-liner to decode using xxd + perl/python.

---

## 🎯 Flag Format

```
cmd=<short>
```

Example: `cmd=python3 -c 'import sys; d=open("e.bin","rb").read(); sys.stdout.buffer.write(bytes(b^0x42 for b in d))'`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Iterate each byte and XOR.
</details>

<details>
<summary>Hint 2</summary>

Or use `xortool` for unknown keys.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
python3 -c 'import sys; d=open("e.bin","rb").read(); sys.stdout.buffer.write(bytes(b^0x42 for b in d))' > p.bin
```

Yields plaintext file.

**Answer:** `cmd=python3 ... XOR with 0x42`

📖 Ref: [playbooks/crypto-playbook.md](../../playbooks/crypto-playbook.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q117".
```
