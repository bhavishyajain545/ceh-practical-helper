# 15 Mobile — full walkthroughs

---

## Walkthrough 1: "Find the hardcoded API key in app.apk"

**Type:** static analysis | **Difficulty:** easy | **Time:** 2–4 min

1. Decode:
   ```bash
   apktool d app.apk -o app_out
   ```
2. Grep for common keywords:
   ```bash
   grep -rEi 'api[_-]?key|secret|token' app_out/
   ```
3. Hit:
   ```
   app_out/res/values/strings.xml:
   <string name="api_key">sk_live_51Hf9xYZabc123DEF456</string>
   ```
4. **Answer:** `sk_live_51Hf9xYZabc123DEF456`

**Tip:** if nothing lands via apktool, open the APK in jadx-gui and Ctrl+Shift+F → "api_key".

---

## Walkthrough 2: "What permissions does app.apk request?"

1. Decode:
   ```bash
   apktool d app.apk -o app_out
   ```
2. Read the manifest:
   ```bash
   grep 'uses-permission' app_out/AndroidManifest.xml
   ```
3. Output:
   ```
   <uses-permission android:name="android.permission.INTERNET"/>
   <uses-permission android:name="android.permission.READ_SMS"/>
   <uses-permission android:name="android.permission.SEND_SMS"/>
   <uses-permission android:name="android.permission.READ_CONTACTS"/>
   ```
4. **Answer:** list the permissions (the exam usually wants the "suspicious" one, e.g. `READ_SMS`).

---

## Walkthrough 3: "Recover the Java source of the login activity in app.apk and find where the password is checked"

1. Open in jadx:
   ```bash
   jadx-gui app.apk
   ```
2. Navigate: `Source code → com.example.app → LoginActivity`.
3. Look for an `onClick` / `validate()` method. Typical find:
   ```java
   if (password.equals("Sup3rS3cr3t!")) {
       startActivity(new Intent(this, MainActivity.class));
   }
   ```
4. **Answer:** password check is in `LoginActivity.onClick()`, hardcoded as `Sup3rS3cr3t!`.

**Gotcha:** obfuscated apps (ProGuard) → classes are named `a.b.c`. Grep the whole decompiled tree instead:
```bash
jadx -d out app.apk
grep -r 'equals(' out/sources/ | grep -i pass
```

---

## Walkthrough 4: "Find the backend URL the app talks to"

1. Quick strings dump — sometimes works without any decode:
   ```bash
   strings app.apk | grep -E 'https?://' | sort -u
   ```
2. If too noisy (lots of schemas.android.com), decode first:
   ```bash
   apktool d app.apk -o app_out
   grep -rE 'https?://' app_out/ | grep -v schemas.android.com | grep -v w3.org
   ```
3. Hit:
   ```
   app_out/smali/com/example/app/Api.smali:
     const-string v0, "https://api-prod.example.com/v1/"
   ```
4. **Answer:** `https://api-prod.example.com/v1/`

→ Follow-up exam twist: visit the URL + fuzz it (pivot to [12-web-apps](../12-web-apps/README.md)).
