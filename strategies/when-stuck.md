# 🚧 Atak Gaye? Yeh Padho

> Stuck honi normal hai. Important hai **stuck ke baad kya karte ho.**

## The 5-step stuck recovery

Jab tumhe lage "yaar yeh ho hi nahi raha":

### Step 1: Hath rok. 30 second deep breath.
Literally, hath keyboard se hata. 30 second saans lo. Frustration command typing tezi se badhata hai → galtiyaan badhti hain → aur frustration. Loop break karna hai.

### Step 2: Question 3rd time padho — out loud
Quietly, but read it OUT LOUD. Sun ke. Tumhare brain ka different part activate hota hai. Often 3rd reading mein woh keyword dikhta hai jo missed tha.

### Step 3: Notepad mein likho — "Mai kya try kar chuka hu"
```
Q7 attempts:
- nmap -sV -p 21    → vsftpd 3.0.3 (no known CVE)
- nmap --script vuln -p 21  → no findings
- searched exploitdb vsftpd 3.0.3 → nothing
- tried anonymous FTP → password required
```
Ye step alone kabhi kabhi answer reveal kar deta hai (tum dekh lete ho ki ek branch unexplored hai).

### Step 4: Repo mein keyword Ctrl+F
Question se 1-2 unique keywords lo. Repo mein search karo:
- Master playbook (`playbooks/master-playbook.md`)
- Domain folder
- Tool docs

Ek "obvious" connection miss ho gaya hota hai often.

### Step 5: 25-min mark — DECISION TIME
Timer 25 min hua? **Decision karo:**
- **Continue**: only if you have a specific concrete next step in mind
- **Skip**: if you're just "trying random things"

Skip karna shame nahi hai. Skip karna **strategy** hai.

---

## Specific stuck scenarios → fixes

### "nmap output mein kuch nahi mil raha"
- `-Pn` add karo (ICMP block ho sakta hai)
- `-p-` use karo (non-standard port pe service)
- `sudo` use karo (`-sS`, `-sU`, `-O` need root)
- `-T4` timing aggressive karo
- `--min-rate 1000` add karo

### "Service mil gaya but version detect nahi ho raha"
- `-sV --version-all` (more aggressive)
- `nc <IP> <PORT>` se manual banner grab
- `curl -I http://<IP>` for HTTP headers
- `--script banner -p <PORT>`

### "Exploit chal nahi raha"
- Target version match karta hai? (msf module mein `show options` → `set RHOSTS`)
- LHOST sahi hai? (tum unka local IP toh nahi dia? `ip a` se tunnel/tap interface)
- Firewall blocking outbound shell? Try bind shell payload
- AV blocking? Try encoded payload (`-e x86/shikata_ga_nai -i 5`)
- Module compatible architecture? (x86 vs x64)

### "Hash crack nahi ho raha"
- Mode galat ho sakta hai. `hashid <hash>` re-run karo
- NTLM = mode 1000 (not 1100, not 5500)
- `--username` flag use kiya? (some hashes have `user:hash` format — strip user)
- Wordlist sahi? `/usr/share/wordlists/rockyou.txt` (decompress if `.gz`)
- Try with rules: `hashcat ... -r /usr/share/hashcat/rules/best64.rule`
- Try CrackStation online (free, fast for common hashes)

### "Web app pe directory enum kuch nahi de raha"
- Different wordlist try karo:
  - `dirbuster/directory-list-2.3-medium.txt`
  - `seclists/Discovery/Web-Content/common.txt`
  - `seclists/Discovery/Web-Content/big.txt`
- Extensions add karo: `-x php,html,txt,bak,old`
- gobuster ke saath ffuf bhi try karo (different timing)
- Recursive: `ffuf -u http://target/FUZZ -recursion`

### "SQLi detect ho raha lekin --dump kuch nahi"
- `--current-db` se start karo, phir `-D <db> --tables` step-by-step
- `--batch --random-agent` use karo
- `--level 5 --risk 3` increase karo (slow but thorough)
- WAF? `--tamper=space2comment`
- Manual technique try karo: time-based via `--technique=T`

### "WPA handshake captured but crack nahi ho raha"
- Handshake actually present hai? `aircrack-ng <cap>` checked?
- hashcat mode 22000 (modern) use kiya, na ki 2500 (old)?
- Convert with `hcxpcapngtool`: `hcxpcapngtool -o hash.hc22000 capture.cap`
- Wordlist sahi? rockyou ya specific
- Sometimes the password is NOT in rockyou — try harder wordlists

### "Output samajh nahi aa raha"
- Tool ka doc kholo: `tools/<tool>.md`
- Section dekho jo tumhare command se related ho
- Worked example dekho: `<domain>/walkthroughs.md`
- Last resort: tool ka man page (`man <tool>`)

---

## Stuck DECISION TREE

```
Stuck on Q?
│
├── 5 min se kam stuck → keep trying
│
├── 5-15 min stuck → Repo mein search karo, doc kholo
│
├── 15-25 min stuck → Try a different approach (alternative tool)
│
└── 25+ min stuck
    ├── Concrete next step hai? → 5 more min
    └── Random trying? → SKIP, mark, move on
```

---

## Skip ke baad kya karna hai

1. Notepad mein likho: `Q7 — SKIPPED at 25 min — stuck on hash crack, hashid says 3 types`
2. Best guess answer submit kar do (kabhi kabhi guess sahi hota hai 🎲)
3. Move to next question
4. Phase 4 mein (last 30 min) wapas aana
5. Phase 4 mein bhi nahi nikla → leave it. **6 wrong allowed hain.**

---

## Mental reframe

Stuck pe ye bolne ki aadat dalo:

> "Interesting. Mai kya miss kar raha hu?"

Not:

> "Mujhse nahi ho raha, mai bewakoof hu."

Pehle wala curiosity hai (problem-solving mode). Doosra defeat hai (panic mode).

---

## Stuck-prevention

Sabse better stuck-recovery hai stuck na hona. Prep mein ye karo:
- Har domain ka 1 walkthrough manually karo (na ki padho — actually Parrot mein commands run karo)
- Common errors note karo apne notepad mein
- Tool error messages ko Google karo abhi, exam mein nahi
