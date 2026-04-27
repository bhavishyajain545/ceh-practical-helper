# Q165 — Extract Hidden Message from WAV Audio File

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Steganography |
| **Difficulty** | 🟡 Medium |
| **Tools** | `steghide` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"An audio file `recording.wav` contains a hidden message embedded using steghide. The passphrase is `audiopass`. Extract the hidden file and report the flag."

---

## 🎯 Flag Format

```
flag=<string>
```

Example: `flag=AUDIO_STEG_FLAG`

**⚠️ Format strict hai** — lowercase, no spaces around `=`, semicolon separator where shown.

---

## 💡 Hints

**Hint 1**

Steghide JPEG ke alawa WAV aur AU audio files pe bhi kaam karta hai.

**Hint 2**

`steghide extract -sf recording.wav -p audiopass`

---

## ✅ Solution

```bash
steghide extract -sf recording.wav -p audiopass
cat extracted_secret.txt
```

**Answer:** `flag=WAV_HIDDEN_DATA`

---

## 🤖 Claude Setup Prompt

1. Create WAV file with hidden data:
```bash
# Generate a simple WAV file
sox -n /tmp/recording.wav synth 3 sine 440
echo "WAV_HIDDEN_DATA" > /tmp/audio_secret.txt
steghide embed -cf /tmp/recording.wav -ef /tmp/audio_secret.txt -p audiopass -f
cp /tmp/recording.wav recording.wav
```

Report back: "Lab ready for Q165 — recording.wav with steghide embedded secret ready"
