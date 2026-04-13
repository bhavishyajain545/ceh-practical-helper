# 🧩 Mental Models — Sochne Ke Patterns

> Yeh chapter "exam ke beech kya soch raha hu" ke baare mein hai. Patterns jo experts use karte hain.

## Model 1: "Question is a puzzle, not a test"

**Wrong mindset:** "Yeh test hai. Ya toh aata hai ya nahi."

**Right mindset:** "Yeh ek puzzle hai. Mujhe pieces dhundne hain. Pieces hamesha exist karte hain."

**Why this matters:** Test mode mein tum freeze ho jate ho. Puzzle mode mein tum **explore** karte ho. Puzzle solving creative + iterative hai.

**How to apply:** Stuck pe ye line bolo:
> "Yeh puzzle hai. Pieces target ke andar hain. Mujhe sirf dhundhne hain."

---

## Model 2: "Tool is a flashlight, not a key"

**Wrong:** "Tool answer dega"

**Right:** "Tool tumhe pehle answer dikhayega. Tum extract karoge."

Tools answers nahi dete. Tools **information** dete hain. Tumhara kaam:
1. Sahi tool chuno
2. Sahi command run karo
3. Output mein answer dhundo

If you blindly run a tool and look for "the answer", you'll miss it. **Tum reader ho. Tool sirf torch hai.**

---

## Model 3: "Onion peeling"

**Concept:** Har target ek onion hai. Tum layers peel karte ho:

```
Layer 1: Open ports (nmap)
Layer 2: Service versions (nmap -sV)
Layer 3: Service config (NSE scripts, enum tools)
Layer 4: Vulnerabilities (vuln scan, CVE lookup)
Layer 5: Exploitation (msf, manual)
Layer 6: Post-exploitation (privesc, lateral)
Layer 7: Final answer (flag, hash, password)
```

**Insight:** Tum directly Layer 7 nahi ja sakte. Sequential hai. **Skip karoge toh wapas aana padega.**

When stuck: "Maine kaunsi layer skip ki?"

---

## Model 4: "Question = haystack with one needle"

Tool output 100 lines hota hai. Sahi line **ek** hai.

**Don't:** Read every line.

**Do:** 
1. Question dubara padho
2. Format dekho — "version" → look for VERSION line
3. `grep` use karo: `grep -i version output.txt`
4. Sahi line ko zoom karo

**Mental shortcut:** Tum needle dhundh rahe ho, hay nahi padhna.

---

## Model 5: "Backwards from answer"

**Powerful technique:** Sochke shuru karo "answer kaisa dikhega".

Example: "Find the password of user `john`"
- Answer kaisa hoga? → string of plaintext
- Plaintext password kahan se aata hai? → cracked hash
- Hash kahan se? → Linux /etc/shadow ya Windows SAM
- /etc/shadow access kaise? → root shell ya download
- Root shell kaise? → exploit kuch
- Exploit kya? → scan results dekhne padenge

Now you have a **plan**, not random commands.

---

## Model 6: "Confidence-based ordering"

Har question ko ek confidence score do (1-5):
- 5 = "I know exactly how to solve this"
- 4 = "I know the tool, just need to run"
- 3 = "I think I know"
- 2 = "I have an idea"
- 1 = "No clue"

**Solve in order: 5 → 4 → 3 → 2 → 1.**

**Why:** Easy wins build confidence. Confidence helps hard ones. Reverse order kills morale.

---

## Model 7: "Tools have personalities"

Har tool ka apna "personality" hai. Samjho toh use easy hota hai:

| Tool | Personality |
|---|---|
| **nmap** | The reporter — gives facts |
| **sqlmap** | The hammer — patient, brutal |
| **hydra** | The lockpicker — tries everything |
| **hashcat** | The translator — hash → plaintext |
| **wireshark** | The historian — tells past stories |
| **metasploit** | The mechanic — has tools for every job, but wants exact specs |
| **burp** | The interceptor — sees everything passing through |

**Insight:** When choosing a tool, ask "which personality matches the question?"

- Need a verdict on a target? → nmap (the reporter)
- Need to extract from a database? → sqlmap (the hammer)
- Need to translate a hash? → hashcat (the translator)

---

## Model 8: "Decision = elimination"

When you have 5 possible tools, don't ask "which is best?". Ask "**which can I rule out?**".

Example: hash crack question.
- Hash given? → hashid
- Online crack possible? → CrackStation (1 min check)
- Mode known? → hashcat
- No GPU? → john
- WPA? → hashcat 22000
- Kerberos? → hashcat 18200/13100

You eliminated 4 options in 30 seconds. The 5th is your tool.

---

## Model 9: "Time as currency"

Each minute is a coin. You have 360 coins (6 hours). Each question costs:
- Easy: 5-10 coins
- Medium: 15-20 coins
- Hard: 25-30 coins

**Budget:** 6 easy × 8 = 48; 10 medium × 17 = 170; 4 hard × 28 = 112. Total: **330**. Leaves 30 for verify.

If you spend 60 coins on one question, you've spent the budget for 4 easy questions. **You're trading 4 marks for 1.** Bad trade.

**Mental phrase:** "Yeh question kitne coins lega? Worth it?"

---

## Model 10: "Answer is in the question"

Ye sabse counter-intuitive but powerful hai.

Real CEH questions often **leak the answer** in their wording:
- "Find the version of the **vulnerable** Apache on..." → vuln scan needed
- "What is the **default** credential for..." → default creds DB
- "Which **port** is running an **outdated**..." → version scan + CVE check

Question wording mein clues hote hain. Read **slowly**. Each word is a hint.

---

## Model 11: "When unfamiliar, look familiar"

Question mein kuch weird tool ya cheez aaya jo tumne padha nahi?

**Don't panic.** Probably a wrapper around something familiar.

Example:
- "snmpcheck" → just SNMP enum, use snmpwalk
- "nmapAutomator" → just nmap with presets
- "fierce" → just DNS recon, use dig/dnsrecon
- "smbghost" → SMB vuln, use nmap script

**Mental rule:** New tool name usually = wrapper around old known tool. Search the name in your repo first.

---

## Model 12: "Follow the data"

When you have data (file, hash, image, pcap), think: **what's the obvious next thing to do?**

| Data type | Obvious next |
|---|---|
| Hash | hashid → john/hashcat |
| Image | exiftool → binwalk → steghide |
| pcap | wireshark + filter |
| .zip with password | zip2john → john |
| APK | apktool → strings/jadx |
| Encrypted file | check headers → identify cipher → decrypt |
| Suspicious URL | curl/burp → analyze response |
| Domain name | whois → DNS → subdomain enum |

**Default workflow:** Always start with the obvious tool first. Exotic only if obvious fails.

---

## Model 13: "Two-attempt rule"

When you try a tool and it fails:
- **Attempt 1:** Re-read the command. Did you typo?
- **Attempt 2:** Try the alternative tool/flag from the repo.
- **3rd attempt:** STOP. Question your approach. Maybe wrong tool/strategy.

After 3 attempts on same approach with no progress → **switch strategy**, don't keep banging.

---

## Model 14: "Energy is a resource"

You start exam with 100% mental energy. Each question burns some:
- Easy: -3%
- Medium: -8%
- Hard: -15%
- Stuck loop: -25%

**By question 15, you might be at 30% energy.**

**Manage it:**
- 1-min breaks every hour
- Drink water
- Don't do hard questions back-to-back
- Save easy questions for late game (when energy is low)

---

## Model 15: "The repo is a teammate"

This isn't a solo exam. **You + the repo = team.** The repo holds the knowledge. You hold the judgment.

Don't be ashamed to look up things. **Looking up is the entire point of having the repo open.**

When you look up:
- You retain better
- You're faster than recalling
- You're less likely to make mistakes
- You build pattern recognition for next time

---

## The summary mental anchor

> **"Mai puzzle solver hu. Tools mere flashlight hain. Repo meri teammate hai. Time meri currency hai. Format mera grader hai."**

Yeh ek line yaad rakhna. Exam ke beech repeat karna jab confused ho.
