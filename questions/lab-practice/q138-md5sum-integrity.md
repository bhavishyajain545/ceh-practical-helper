# Q138 — md5sum File Integrity

| Field | Value |
|---|---|
| **Target** | (local sample files) |
| **Domain** | 06 — Malware / Forensics |
| **Difficulty** | 🟢 Easy |
| **Tools** | `strings`, `file`, `peframe`, `olevba`, `yara`, `foremost` |
| **Time budget** | 10–15 min |

---

## 📝 Question

Generate MD5 of `/etc/hosts` and verify with `md5sum -c`. Provide both commands.

---

## 🎯 Flag Format

```
cmds=<short>
```

Example: `cmds=md5sum /etc/hosts > h.md5; md5sum -c h.md5`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

Save then verify.
</details>

<details>
<summary>Hint 2</summary>

Returns OK or FAILED.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
md5sum /etc/hosts > h.md5
md5sum -c h.md5
```

Returns `OK`.

**Answer:** `cmds=md5sum /etc/hosts > h.md5; md5sum -c h.md5`

📖 Ref: [tools/md5sum.md](../../tools/md5sum.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Verify VMs running: ping 192.168.52.129 and ping 192.168.52.130 from 192.168.52.128.
2. Required services for this Q already up by default on the relevant target.
3. If something is down, restart it on the target VM.

Report back: "Lab ready for Q138".
```
