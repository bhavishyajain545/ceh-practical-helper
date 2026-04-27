# Q250 — Multi-Step Hash Crack Chain

| Field | Value |
|-------|-------|
| **Target** | local / `192.168.52.129` |
| **Domain** | Cryptography / Hash Analysis |
| **Difficulty** | 🔴 Hard |
| **Tools** | `hashid, john, hashcat` |
| **Time budget** | 15–20 min |

---

## 📝 Question

"A file `hashes_mixed.txt` has 3 different hash types mixed together. For each: (1) identify the type, (2) select correct tool/mode, (3) crack it, (4) use the cracked password to login to SSH on target."

---

## 🎯 Flag Format

```
hash1_pass=<pass>; hash2_pass=<pass>; hash3_pass=<pass>; ssh_flag=<string>
```

---

## ✅ Solution

```bash
hashid -m -f hashes_mixed.txt
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash1.txt
john --format=raw-sha1 --wordlist=/usr/share/wordlists/rockyou.txt hash2.txt
john --format=nt --wordlist=/usr/share/wordlists/rockyou.txt hash3.txt
ssh user@192.168.52.129
cat /root/flag.txt
```

**Answer:** `hash1_pass=123456; hash2_pass=password; hash3_pass=admin; ssh_flag=HASH_CHAIN_COMPLETE`

---

## 🤖 Claude Setup Prompt

```bash
echo -n "123456" | md5sum | awk '{print $1}' > hashes_mixed.txt
echo -n "password" | sha1sum | awk '{print $1}' >> hashes_mixed.txt
echo "a4f49c406510bdcab6824ee7c30fd852" >> hashes_mixed.txt
```

Report back: "Lab ready for Q250 — mixed hashes file ready"
