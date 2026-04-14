# Q015 — Source Port Manipulation

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Some firewalls allow port 53 (DNS) outbound. Run an nmap scan from source port 53 against Metasploitable.

---

## 🎯 Flag Format

```
flag=<nmap-flag>
```

Example: `flag=--source-port 53`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-g 53` or `--source-port 53`.
</details>

<details>
<summary>Hint 2</summary>

Useful to bypass simple ACL firewalls.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo nmap --source-port 53 -sS 192.168.52.129
```

Scan completes using src port 53.

**Answer:** `flag=--source-port 53`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q015:
1. Verify Metasploitable2 (192.168.52.129) is reachable: ping -c 2 192.168.52.129.
2. --source-port / -g binds a specific src port → requires raw packets with -sS → sudo on Parrot. Confirm: sudo -n true || sudo -v.
3. Ensure nothing on Parrot is already using UDP/TCP port 53 that would conflict — if you run a local DNS resolver (systemd-resolved, dnsmasq) it typically binds 127.0.0.53:53 only, which is fine. Sanity check: sudo ss -lntp | grep ':53 ' — should show nothing on the 192.168.52.128 interface.
4. This Q is primarily recall/syntax — the nmap scan just needs to complete without error to verify the flag is accepted.

Report back: "Lab ready for Q015 — 192.168.52.129 reachable, sudo works, port 53 free on Parrot's lab IP".
```
