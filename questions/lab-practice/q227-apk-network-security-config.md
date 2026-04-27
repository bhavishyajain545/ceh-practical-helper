# Q227 — Analyze Network Security Config

| Field | Value |
|-------|-------|
| **Target** | local |
| **Domain** | Mobile Hacking |
| **Difficulty** | 🟡 Medium |
| **Tools** | `apktool` |
| **Time budget** | 5–10 min |

---

## 📝 Question

"Decompile `banking.apk` and check the network security configuration. Does the app allow cleartext HTTP traffic? Does it pin any certificates? Report the security posture."

---

## 🎯 Flag Format

```
cleartext=<yes|no>; cert_pinning=<yes|no>; flag=<string>
```

---

## 💡 Hints

**Hint 1**

Check `res/xml/network_security_config.xml` after decompiling.

**Hint 2**

`cleartextTrafficPermitted="true"` means HTTP allowed — insecure!

---

## ✅ Solution

```bash
apktool d banking.apk -o decompiled
cat decompiled/res/xml/network_security_config.xml
grep -i "cleartext\|pin-set\|trust-anchors" decompiled/res/xml/network_security_config.xml
```

**Answer:** `cleartext=yes; cert_pinning=no; flag=INSECURE_NETWORK_CONFIG`

---

## 🤖 Claude Setup Prompt

Provide APK with insecure network_security_config.xml.

Report back: "Lab ready for Q227 — banking.apk with network security config ready"
