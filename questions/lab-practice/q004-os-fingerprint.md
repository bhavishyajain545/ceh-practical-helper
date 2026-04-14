# Q004 — OS Fingerprinting Metasploitable

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap` |
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
Pre-requisites for Q004:
1. Verify Metasploitable2 (192.168.52.129) is reachable: ping -c 2 192.168.52.129.
2. OS detection (-O) requires raw packets — sudo on Parrot required. Confirm: sudo -n true || sudo -v.
3. Nmap needs at least 1 open AND 1 closed TCP port on the target for reliable fingerprinting — Metasploitable satisfies this by default (many open ports + some closed in top 1000). No tuning needed.
4. Expected result: Linux 2.6.x kernel family.

Report back: "Lab ready for Q004 — 192.168.52.129 reachable, sudo available".
```
