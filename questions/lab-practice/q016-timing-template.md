# Q016 — Timing Template T4

| Field | Value |
|---|---|
| **Target** | `192.168.52.129` (Metasploitable 2) |
| **Domain** | 02 — Scanning |
| **Difficulty** | 🟢 Easy |
| **Tools** | `nmap` |
| **Time budget** | 5–10 min |

---

## 📝 Question

Run an aggressive-timing nmap scan (T4) against Metasploitable. Report scan duration in seconds (approx).

---

## 🎯 Flag Format

```
timing=<T0-T5>; rough_seconds=<n>
```

Example: `timing=T4; rough_seconds=15`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

<details>
<summary>Hint 1 (click)</summary>

`-T4` is aggressive but reliable on LAN.
</details>

<details>
<summary>Hint 2</summary>

Time appears at end: `Nmap done: 1 IP address ... scanned in X.Xs`.
</details>

---

## ✅ Solution

<details>
<summary>Show solution</summary>

```bash
sudo nmap -T4 -sS 192.168.52.129
```

On LAN, completes in ~10-20s.

**Answer:** `timing=T4; rough_seconds=15`

📖 Ref: [tools/nmap.md](../../tools/nmap.md)
</details>

---

## 🤖 Claude Setup Prompt (for Claude-on-your-PC)

```
Pre-requisites for Q016:
1. Verify Metasploitable2 (192.168.52.129) is reachable: ping -c 2 192.168.52.129.
2. -sS with -T4 → raw packets → sudo required on Parrot. Confirm: sudo -n true || sudo -v.
3. For a clean timing number, no competing traffic on the lab NIC — close Wireshark captures, bulk downloads, etc. This keeps wall-clock close to the expected ~10-20s on LAN.
4. If you want to compare, also run with -T3 (default) and -T5 (insane, may miss ports) and note the delta. Put the numbers in the answer if the exam asks for them.

Report back: "Lab ready for Q016 — 192.168.52.129 reachable, sudo works, lab NIC quiet".
```
