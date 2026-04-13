# Question Bank — Mobile (10 questions)

> APK static analysis questions. Most CEH mobile Qs are "open the APK, find string X". You rarely need a running device.

**Legend:** 🟢 easy · 🟡 medium · 🔴 hard

---

### Q1 🟢 — Decompile app.apk to inspect its resources

**Category:** APK unpacking | **Tools:** [apktool](../../tools/apktool.md)

**Steps:**
1. `apktool d app.apk -o app_out` ← [why d](../../tools/apktool.md#decode)
2. `ls app_out/` — you'll see `AndroidManifest.xml`, `res/`, `smali/`.

**Answer format:** path to the decoded folder (or confirmation the manifest is readable XML now, not binary).

**Gotcha:** raw `unzip app.apk` gives you the APK files but `AndroidManifest.xml` stays in binary AXML — you must use apktool to read it.

---

### Q2 🟢 — Find the package name of app.apk

**Category:** Manifest parsing | **Tools:** [apktool](../../tools/apktool.md), `grep`

**Steps:**
1. `apktool d app.apk -o app_out`
2. `grep -oP 'package="\K[^"]+' app_out/AndroidManifest.xml`
3. Or open `apktool.yml` — the `packageInfo:` block lists it.

**Answer format:** reverse-DNS string (e.g. `com.example.vulnapp`)

**Gotcha:** the package name is NOT always the same as the main activity's parent package — read the `package=` attribute on the root `<manifest>` tag.

---

### Q3 🟢 — List all permissions requested by app.apk

**Category:** Manifest parsing | **Tools:** [apktool](../../tools/apktool.md), `grep`

**Steps:**
1. `apktool d app.apk -o app_out`
2. `grep uses-permission app_out/AndroidManifest.xml`

**Answer format:** count, or a specific permission (e.g. `android.permission.SEND_SMS`)

**Gotcha:** CEH often asks "is permission X dangerous?" — SMS, CONTACTS, LOCATION, CAMERA, RECORD_AUDIO, READ/WRITE_EXTERNAL_STORAGE are "dangerous".

---

### Q4 🟡 — Find hardcoded API keys or URLs inside app.apk

**Category:** Static secrets | **Tools:** [apktool](../../tools/apktool.md), `grep`

**Steps:**
1. `apktool d app.apk -o app_out`
2. Check strings.xml:
   ```bash
   grep -iE "api|key|token|secret|http" app_out/res/values/strings.xml
   ```
3. Also grep the smali tree:
   ```bash
   grep -rnE "https?://|AKIA|sk_live_|Bearer " app_out/smali/
   ```

**Answer format:** the literal string (URL or key).

**Gotcha:** many apps store keys base64'd — run `base64 -d` on suspicious blobs.

---

### Q5 🟡 — Decompile app.apk to readable Java

**Category:** Decompilation | **Tools:** [jadx](../../tools/jadx.md)

**Steps:**
1. GUI: `jadx-gui app.apk`
2. CLI: `jadx -d app_src app.apk` ← [why -d](../../tools/jadx.md#cli)
3. Browse `app_src/sources/<package>/` for `.java`.

**Answer format:** path / class name / code snippet as asked.

**Gotcha:** jadx is for Java source, apktool is for manifest + resources + smali. You usually need BOTH.

---

### Q6 🟡 — Find the main activity of app.apk

**Category:** Manifest parsing | **Tools:** [apktool](../../tools/apktool.md)

**Steps:**
1. `apktool d app.apk -o app_out`
2. `grep -B1 -A3 "MAIN" app_out/AndroidManifest.xml`
3. The `<activity android:name="...">` with intent-filter `android.intent.action.MAIN` + category `LAUNCHER` is it.

**Answer format:** fully-qualified class name (e.g. `com.example.vulnapp.LoginActivity`)

**Gotcha:** if the name starts with `.`, prepend the package name.

---

### Q7 🟡 — Is app.apk debuggable?

**Category:** Manifest flags | **Tools:** [apktool](../../tools/apktool.md)

**Steps:**
1. `apktool d app.apk -o app_out`
2. `grep -i "android:debuggable" app_out/AndroidManifest.xml`
3. Also check `android:allowBackup` — both are common CEH flag questions.

**Answer format:** `true` / `false`

**Gotcha:** if the attribute is missing, the default is `false`. Only answer `true` if you literally see `android:debuggable="true"`.

---

### Q8 🔴 — Find hardcoded credentials in app.apk smali

**Category:** Static secrets | **Tools:** [apktool](../../tools/apktool.md), `grep`, [jadx](../../tools/jadx.md)

**Steps:**
1. `apktool d app.apk -o app_out`
2. Search smali for string literals:
   ```bash
   grep -rnE "const-string.*(pass|pwd|user|login|admin)" app_out/smali/
   ```
3. Cross-check in jadx GUI — right-click a suspect method -> "Find Usage".

**Answer format:** `user:password` pair exactly as stored.

**Gotcha:** smali string literals look like `const-string v0, "admin123"`. Grep for `const-string` to see every embedded string.

---

### Q9 🔴 — Convert app.apk's DEX to a JAR for inspection

**Category:** DEX to JAR | **Tools:** [dex2jar](../../tools/dex2jar.md)

**Steps:**
1. `d2j-dex2jar app.apk -o app.jar` ← [why d2j](../../tools/dex2jar.md#usage)
2. Open `app.jar` in `jd-gui app.jar` for a second opinion on obfuscated classes.

**Answer format:** path to the jar, or the class/method the question asks for.

**Gotcha:** use this when jadx fails on a heavily obfuscated APK — sometimes JD-GUI renders what jadx can't.

---

### Q10 🔴 — Run a full static scan of app.apk with MobSF

**Category:** Automated static | **Tools:** [MobSF](../../tools/mobsf.md)

**Steps:**
1. Start MobSF: `docker run -it --rm -p 8000:8000 opensecurity/mobile-security-framework-mobsf`
2. Open `http://127.0.0.1:8000`, drag app.apk in.
3. Read the report's **Manifest Analysis**, **Code Analysis**, **Hardcoded Secrets** sections.

**Answer format:** the finding MobSF surfaces (CVSS, permission, secret, tracker).

**Gotcha:** MobSF is slow to boot (~30s). Start it at the beginning of the exam if you suspect a mobile Q is coming.

---

## 📌 Quick links

- [apktool reference](../../tools/apktool.md)
- [jadx reference](../../tools/jadx.md)
- [dex2jar reference](../../tools/dex2jar.md)
- [MobSF reference](../../tools/mobsf.md)
- [15-mobile README](../../15-mobile/README.md)
