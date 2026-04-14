# Q014 — Fragmented Packets Scan

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap`, `hping3`, `masscan`, `nc` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Use nmap `-f` against Metasploitable to send fragmented probes. What is the default fragment size used by `-f`?

---

## 🎯 Flag Format

```
frag_bytes=<n>
```

Example: `frag_bytes=8`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-f` fragments to 8-byte chunks; `-ff` to 16.
</details>

<details>
<summary>Hint 2</summary>

Custom MTU via `--mtu <n>` (multiple of 8).
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo nmap -f -sS 192.168.52.129
```

Default is **8 bytes** per fragment.

**Answer:** `frag_bytes=8`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Metasploitable up.

Report back: "Lab ready for Q014".
```
