# 15 — Mobile (Android APK)

> CEH Practical mobile questions almost always hand you an APK and ask for a hardcoded string, an API key, a URL, or a secret in the manifest. **Static analysis** is your friend.

## 🧭 Decision tree — "I see a mobile question"

```
APK file provided?
│
├── "Find a hardcoded credential / API key / URL"
│   ├── apktool d <APK>                    → smali + res/ + AndroidManifest.xml
│   ├── grep -rE 'http|api|key|token|pass' <out>/
│   └── Check res/values/strings.xml
│
├── "Get the Java source"
│   ├── jadx-gui <APK>                     → GUI decompiler (easiest)
│   └── jadx -d out <APK>                  → CLI dump
│
├── "What permissions does it ask for"
│   └── Read AndroidManifest.xml → <uses-permission> lines
│
├── "What activities / services are exported"
│   └── Read AndroidManifest.xml → android:exported="true"
│
├── "Alternative: dex2jar + JD-GUI"
│   ├── d2j-dex2jar <APK>
│   └── jd-gui classes-dex2jar.jar
│
└── "Install + runtime test"
    ├── adb install <APK>
    ├── adb shell → pm list packages
    └── adb logcat | grep <package>
```

## 📄 Files in this folder

- **[commands.md](commands.md)** — apktool / jadx / adb recipes
- **[walkthroughs.md](walkthroughs.md)** — full APK extraction examples
- Question bank: **[../questions/15-mobile.md](../questions/15-mobile.md)**

## 🛠 Tools used in this domain

- **[apktool](../tools/apktool.md)** — decode resources + smali
- **[jadx](../tools/jadx.md)** — best-in-class decompiler
- **[dex2jar](../tools/dex2jar.md)** — convert dex → jar
- **[mobsf](../tools/mobsf.md)** — automated static + dynamic framework
- **[adb](../tools/adb.md)** — Android Debug Bridge

## ✅ Domain checklist

- [ ] Decode an APK with apktool
- [ ] Open an APK in jadx-gui and navigate to `MainActivity`
- [ ] Grep resources for `api`, `key`, `http`, `password`
- [ ] Read `AndroidManifest.xml` — permissions + exported components
- [ ] Know where `strings.xml` lives (`res/values/strings.xml`)
- [ ] Run an APK through MobSF (upload + read report)
- [ ] Done all questions in [the question bank](../questions/15-mobile.md)
