# XSS Log Detection — SOC L1

## What is XSS?

**XSS (Cross-Site Scripting)** is a web security vulnerability where attacker-controlled input can cause JavaScript to execute in a user's browser.

For a **SOC L1 analyst**, the main goal is not exploiting XSS. The goal is to **recognize suspicious XSS attempts in web logs and investigate them**.

---

## What Does XSS Look Like in Logs?

Example:

```text
GET /search?q=<script>alert(1)</script> HTTP/1.1
```

The suspicious part is:

```html
<script>alert(1)</script>
```

This indicates an attempt to inject JavaScript.

---

## URL-Encoded XSS

Attackers may URL-encode their payloads.

Example:

```text
GET /search?q=%3Cscript%3Ealert(1)%3C%2Fscript%3E HTTP/1.1
```

Decoded:

```html
<script>alert(1)</script>
```

Useful encoding examples:

| Encoded | Character |
| ------- | --------- |
| `%3C`   | `<`       |
| `%3E`   | `>`       |
| `%2F`   | `/`       |
| `%20`   | space     |

**Important:** Encoding itself is not malicious. The decoded content must be examined.

---

## Common XSS Indicators

As an L1 analyst, look for suspicious patterns such as:

```text
<script>
</script>
javascript:
onerror=
onload=
<svg
<iframe
```

These are indicators, not automatic proof of an attack.

---

## Example Log

```text
127.0.0.1 - - [16/Aug/2026 13:41:13]
"GET /search?q=%3Cscript%3Ealert(1)%3C%2Fscript%3E HTTP/1.1" 404 -
```

### L1 Analysis

**Source IP:**

```text
127.0.0.1
```

The request came from the local machine in our practice environment.

**HTTP method:**

```text
GET
```

**Requested path:**

```text
/search
```

**Parameter:**

```text
q=
```

**Payload:**

```text
%3Cscript%3Ealert(1)%3C%2Fscript%3E
```

After decoding:

```html
<script>alert(1)</script>
```

**HTTP status:**

```text
404
```

The requested `/search` resource was not found on our Python test server.

### L1 conclusion

> The request contains a URL-encoded XSS payload in the `q` parameter. The request is suspicious and should be investigated as a potential XSS attempt. The server returned HTTP 404, meaning the requested resource was not found.

---

## Practicing XSS Detection on Linux

Start a simple local HTTP server:

```bash
python3 -m http.server 8000
```

Send a normal request:

```bash
curl "http://127.0.0.1:8000/search?q=hello"
```

Send an XSS-looking request:

```bash
curl "http://127.0.0.1:8000/search?q=%3Cscript%3Ealert(1)%3C%2Fscript%3E"
```

Search a saved log for encoded `<script>`:

```bash
grep -i "%3cscript%3e" xss.log
```

---

## L1 Investigation Process

When an XSS alert appears:

```text
1. Identify the source IP
        ↓
2. Check timestamp
        ↓
3. Examine requested URL and parameters
        ↓
4. Look for XSS indicators
        ↓
5. Decode encoded input
        ↓
6. Check HTTP response status
        ↓
7. Look for repeated/similar requests
        ↓
8. Determine whether activity is suspicious
        ↓
9. Escalate according to SOC procedures
```

---

## Important Status Codes

| Status    | Meaning            |
| --------- | ------------------ |
| `200`     | Request successful |
| `301/302` | Redirect           |
| `400`     | Bad request        |
| `403`     | Forbidden          |
| `404`     | Resource not found |
| `500`     | Server-side error  |

A `404` response **does not automatically mean the request was harmless**. A suspicious XSS payload can still indicate an attack attempt even if the requested resource was not found.

---

## L1 Key Takeaways

* XSS = Cross-Site Scripting.
* Look for suspicious JavaScript/HTML injection patterns.
* Pay attention to **user-controlled parameters**.
* Attackers may URL-encode payloads.
* Decode/normalize suspicious input before analyzing it.
* Encoding alone does not mean something is malicious.
* Check the source IP, timestamp, URL, parameters, and response status.
* `404` means **Not Found**, not necessarily "no attack."
* L1 analysts primarily **detect, analyze, document, and escalate** rather than perform advanced exploitation.
