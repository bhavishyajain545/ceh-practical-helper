# apktool — decode & rebuild Android APKs

> **The smali-level APK editor.** Decodes resources + bytecode, lets you patch, then rebuilds + signs.

**Install check:** `apktool --version`

---

## 🎯 Cheat-flow

```bash
apktool d app.apk                 # decode → ./app/
# edit AndroidManifest.xml, smali/, res/
apktool b app -o patched.apk      # rebuild
# sign it (rebuilt APKs are unsigned):
jarsigner -keystore debug.keystore patched.apk androiddebugkey
```

---

## 🔑 Commands / flags

| Command | Purpose |
|---|---|
| `apktool d <apk>` | Decode |
| `apktool d -s <apk>` | Decode **without** disassembling dex (keeps classes.dex) |
| `apktool d -r <apk>` | Don't decode resources |
| `apktool d -f <apk>` | Force overwrite existing folder |
| `apktool d -o <dir> <apk>` | Output dir |
| `apktool b <dir>` | Build APK from folder |
| `apktool b <dir> -o <out.apk>` | Build to explicit file |
| `apktool if <framework.apk>` | Install framework (vendor system apps) |
| `apktool empty-framework-dir` | Clear framework cache |

---

## 📂 What you get after `d`

```
app/
├── AndroidManifest.xml       # permissions, activities, exported flags
├── apktool.yml               # metadata
├── res/                      # strings, layouts, drawables
├── smali/                    # disassembled Dalvik bytecode
├── assets/
└── original/META-INF/        # original signature (useful hashes)
```

Useful grep targets:
```bash
grep -R "http://"  app/res/values/strings.xml
grep -R "api_key\|apikey\|secret" app/
grep -R "android:exported=\"true\"" app/AndroidManifest.xml
```

---

## 📋 Recipes

```bash
# 1. Just list strings / manifest (fast, no smali)
apktool d -s app.apk

# 2. Patch an Activity's smali and rebuild
apktool d app.apk
# edit app/smali/com/example/LoginActivity.smali
apktool b app -o patched.apk

# 3. Sign with debug key so emulator will install
keytool -genkey -v -keystore debug.keystore -alias androiddebugkey \
        -storepass android -keypass android -keyalg RSA -validity 10000
jarsigner -sigalg SHA1withRSA -digestalg SHA1 \
          -keystore debug.keystore -storepass android \
          patched.apk androiddebugkey
adb install -r patched.apk
```

---

## ⚠️ Gotchas

- Rebuilt APKs are **unsigned** → install fails on Android 7+ without `apksigner`/`jarsigner`.
- If you only need Java code (not smali), use [jadx](jadx.md) instead.
- Some obfuscated apps fail to rebuild — try `-s` to keep original dex.
- Resource decode errors → install matching framework: `apktool if framework-res.apk`.
- Large heap required for big APKs: `export _JAVA_OPTIONS="-Xmx2g"`.

---

## 🔗 Related

- [jadx](jadx.md) · [dex2jar](dex2jar.md) · [mobsf](mobsf.md) · [adb](adb.md)
