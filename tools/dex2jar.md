# dex2jar — DEX → JAR converter

> **Bridge to Java analysis tools.** Converts Android DEX bytecode to a JAR you can open in JD-GUI, Bytecode Viewer, IntelliJ.

**Install check:** `d2j-dex2jar.sh --version`

---

## 🎯 Cheat-flow

```bash
unzip app.apk classes.dex                  # pull DEX out of APK
d2j-dex2jar.sh app.apk -o app.jar          # APK → JAR directly
d2j-dex2jar.sh classes.dex -o classes.jar
jd-gui app.jar &                           # open in JD-GUI
```

Jar → source (CLI):
```bash
javap -c -p com.example.MainActivity       # disassemble .class
# or use procyon / cfr / fernflower for decompilation
cfr app.jar --outputdir src/
```

---

## 🔑 Sister tools in the suite

| Tool | Purpose |
|---|---|
| `d2j-dex2jar` | DEX → JAR (main one) |
| `d2j-jar2dex` | JAR → DEX (re-pack) |
| `d2j-apk-sign` | Sign an APK |
| `d2j-asm-verify` | Verify bytecode |
| `d2j-baksmali` / `d2j-smali` | Alt smali assembler/disassembler |
| `d2j-decrypt-string` | Decrypt strings in obfuscated apps |

---

## ⚠️ Gotchas

- Fails silently on multi-dex apps — pass each `classes*.dex` or use the APK directly.
- Prefer [jadx](jadx.md) for direct APK → Java (single step, cleaner).
- Errors about `Couldn't read X` usually mean the dex is packed/encrypted — unpack first.
- Output JAR is Java bytecode; still need a decompiler (JD-GUI, CFR, Procyon) to read as Java.

---

## 🔗 Related

- [jadx](jadx.md) · [apktool](apktool.md) · [mobsf](mobsf.md) · [adb](adb.md)
