# jadx — Dex to Java decompiler

> **The easiest way to read Android app source.** Decompiles APK/DEX straight to readable Java.

**Install check:** `jadx --version`, `jadx-gui --version`

---

## 🎯 Cheat-flow

```bash
jadx-gui app.apk                        # GUI — search, xref, deobfuscate
jadx -d out app.apk                     # CLI decompile → ./out/sources/ + resources/
jadx -d out --no-res app.apk            # skip resources
jadx -d out --deobf app.apk             # rename obfuscated identifiers
```

---

## 🔑 Flags

| Flag | Meaning |
|---|---|
| `-d <dir>` | Output directory |
| `-r` / `--no-res` | Don't decode resources |
| `-s` / `--no-src` | Only resources, skip sources |
| `-e` | Export as Gradle project |
| `--deobf` | Rename obfuscated classes/methods |
| `--deobf-min <n>` | Only deobfuscate names shorter than n |
| `--show-bad-code` | Show code with errors instead of skipping |
| `-j <N>` | Threads |
| `--log-level ERROR` | Quiet |

---

## 🧪 jadx-gui search tips (where the money is)

`Ctrl+Shift+F` → **Text search**. Keywords that usually pay:

| Keyword | Why |
|---|---|
| `http://` / `https://` | Hardcoded API endpoints |
| `api_key`, `apikey`, `secret`, `token` | Hardcoded creds |
| `BuildConfig` | Debug flags, build-time secrets |
| `password`, `passwd` | Obvious |
| `Firebase` | Firebase URLs (often world-readable) |
| `AWS_`, `s3.amazonaws` | Cloud keys |
| `onCreate` | Activity entrypoints |
| `Cipher.getInstance` | Crypto — weak modes, hardcoded keys |
| `Log.d` / `Log.v` | Debug logs left in prod |

`Ctrl+F` inside a class = find in file. Right-click → **Find Usage** for xref.

---

## 📋 Recipes

```bash
# 1. CLI dump to disk (grep-friendly)
jadx -d out --deobf app.apk
grep -R "api_key\|https\?://" out/sources/ | head

# 2. Open a single DEX
jadx -d out classes.dex

# 3. Export as Gradle project (import into Android Studio)
jadx -d out -e app.apk
```

---

## ⚠️ Gotchas

- Not everything decompiles — complex lambdas / obfuscated code may show `// jadx error` blocks. Re-run with `--show-bad-code` to inspect.
- For *modifying* the app, use [apktool](apktool.md) (smali) — jadx output is read-only Java.
- Large APKs: increase heap → `export JAVA_OPTS="-Xmx4g"`.
- jadx-gui remembers last project — `File → Reset settings` if it crashes on open.

---

## 🔗 Related

- [apktool](apktool.md) · [dex2jar](dex2jar.md) · [mobsf](mobsf.md) · [adb](adb.md) · [strings](strings.md)
