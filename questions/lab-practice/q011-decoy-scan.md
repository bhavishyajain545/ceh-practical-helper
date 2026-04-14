# Q011 — Decoy Scan

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟡 Medium |
| **Tools** | `nmap` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Run an nmap scan with **5 decoys** against Metasploitable. Provide the exact flag combo used.

---

## 🎯 Flag Format

```
flag=<nmap-flag>
```

Example: `flag=-D RND:5`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-D` lets you spoof source IPs; `RND:N` generates N random decoys.
</details>

<details>
<summary>Hint 2</summary>

Combine with `-sS` for stealth.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo nmap -sS -D RND:5 192.168.52.129
```

Scan succeeds with 5 random decoy source addresses.

**Answer:** `flag=-D RND:5`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q011:
1. Verify Metasploitable2 (192.168.52.129) is reachable: ping -c 2 192.168.52.129.
2. Decoy scan (-D) crafts spoofed source IPs → needs raw packets → sudo required. Confirm: sudo -n true || sudo -v.
3. No egress filter should drop the spoofed packets — on an isolated VMware host-only / NAT segment this is fine by default. If using a bridged adapter, the host router may drop spoofed src and only your real IP will reach target (decoys look like they don't arrive).
4. No kernel tuning required for -D RND:5 on Linux (unlike idle scan, decoy doesn't need predictable IPID).

Report back: "Lab ready for Q011 — 192.168.52.129 reachable, sudo works".
```
