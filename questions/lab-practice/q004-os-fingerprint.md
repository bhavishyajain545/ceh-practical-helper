# Q004 — OS Fingerprinting Metasploitable

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap`, `hping3`, `masscan`, `nc` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Use nmap OS detection to determine the **OS family** and **kernel version range** of the Metasploitable host.

---

## 🎯 Flag Format

```
os_family=<linux|windows>; kernel=<x.y>
```

Example: `os_family=linux; kernel=2.6`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-O` flag enables OS detection (needs root).
</details>

<details>
<summary>Hint 2</summary>

Combine with `-sS -sV` for richer fingerprint.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo nmap -O 192.168.52.129
```

Reports Linux 2.6.x kernel.

**Answer:** `os_family=linux; kernel=2.6`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
1. Metasploitable up. 2. Run as root from Parrot.

Report back: "Lab ready for Q004".
```
