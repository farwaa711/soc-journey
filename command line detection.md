# Command Injection Log Detection — SOC L1

## What is Command Injection?

**Command injection** is a vulnerability where attacker-controlled input may cause an application to execute operating-system commands.

For a **SOC L1 analyst**, the main goal is not exploiting the vulnerability. The goal is to:

* Recognize suspicious command-injection attempts.
* Identify the evidence in logs.
* Determine whether the activity appears to be an attempt or possible successful execution.
* Document and escalate according to SOC procedures.

---

## What Does Command Injection Look Like in Logs?

Example:

```text
GET /ping?host=127.0.0.1%3Bwhoami HTTP/1.1
```

The suspicious input is:

```text
127.0.0.1%3Bwhoami
```

`%3B` is URL encoding for:

```text
;
```

After decoding:

```text
127.0.0.1;whoami
```

The combination of a command-separator character and an OS command is suspicious.

---

## Common Indicators

Command-injection attempts may contain shell metacharacters such as:

```text
;
&&
||
|
`
$(
```

They may also contain operating-system command names such as:

```text
whoami
id
uname
pwd
ls
cat
```

**Important:** these words or characters alone do not prove an attack. Context is important.

---

## Example Log

```text
127.0.0.1 - - [16/Aug/2026 22:56:00]
"GET /ping?host=127.0.0.1%3Bwhoami HTTP/1.1" 404 -
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

**Endpoint:**

```text
/ping
```

**Parameter:**

```text
host=
```

**Suspicious input:**

```text
127.0.0.1%3Bwhoami
```

Decoded:

```text
127.0.0.1;whoami
```

The `%3B` represents `;`, which can act as a command separator in some shell contexts.

`whoami` is an operating-system command.

**HTTP status:**

```text
404
```

The requested `/ping` resource was not found on our Python test server.

### L1 conclusion

> The request contains a URL-encoded command-injection-like payload in the `host` parameter. The request is suspicious and should be investigated as a potential command-injection attempt. The HTTP 404 response does not prove that command execution occurred.

---

## Evidence Created by a Command Injection Attempt

A SOC analyst should look for evidence across multiple sources.

### 1. Web/Application Logs

May show:

```text
GET /ping?host=127.0.0.1%3Bwhoami
```

Useful evidence:

* Source IP
* Timestamp
* HTTP method
* Endpoint
* Parameters
* Encoded payload
* User-Agent
* HTTP response status

### 2. System/Process Logs

If execution actually occurred, other telemetry may show:

```text
Web application
      ↓
Unexpected process
      ↓
Operating-system activity
```

For example, security monitoring may show an unexpected process launched by a web server or application process.

This is stronger evidence than the HTTP request alone.

---

## Attempt vs. Successful Execution

This distinction is very important for SOC L1.

### Evidence of an attempt

```text
GET /ping?host=127.0.0.1%3Bwhoami
```

This tells us:

> Someone sent suspicious input.

It does **not** prove the server executed the command.

### Stronger evidence of possible execution

You may also find:

* Unexpected child process creation.
* Suspicious process spawned by a web application.
* Corresponding system/security events.
* Application errors occurring immediately after the request.
* Repeated requests followed by unusual server activity.

The more independent evidence you have, the stronger the conclusion.

---

## URL Encoding

Attackers may encode characters in HTTP requests.

Examples:

| Encoded | Character |   |
| ------- | --------- | - |
| `%3B`   | `;`       |   |
| `%26`   | `&`       |   |
| `%7C`   | `         | ` |
| `%60`   | `` ` ``   |   |
| `%24`   | `$`       |   |

Therefore, SOC analysts should consider **decoding/normalization** when investigating suspicious requests.

Encoding itself is **not malicious**. The decoded context must be examined.

---

## Linux Practice

Start a local HTTP server:

```bash
python3 -m http.server 8000
```

Send a harmless command-injection-looking request:

```bash
curl "http://127.0.0.1:8000/ping?host=127.0.0.1%3Bwhoami"
```

The Python server logs the HTTP request.

Search a saved log for suspicious command-related strings:

```bash
grep -i "whoami" xss.log
```

For a real SOC environment, the log filename would be replaced with the organization's actual web-server or SIEM data source.

---

## L1 Investigation Process

```text
Alert / suspicious log
        ↓
Identify source IP
        ↓
Check timestamp
        ↓
Examine endpoint and parameters
        ↓
Look for command-injection indicators
        ↓
Decode encoded input
        ↓
Check HTTP response
        ↓
Look for repeated attempts
        ↓
Check application/system/process telemetry
        ↓
Determine attempt vs. possible execution
        ↓
Document and escalate
```

---

## Important HTTP Status Codes

| Status    | Meaning            |
| --------- | ------------------ |
| `200`     | Request successful |
| `301/302` | Redirect           |
| `400`     | Bad request        |
| `403`     | Forbidden          |
| `404`     | Resource not found |
| `500`     | Server-side error  |

A `404` response **does not automatically mean the activity was harmless**.

---

## Command Injection vs. XSS

### XSS

Look for indicators such as:

```text
<script>
onerror=
onload=
javascript:
<svg
```

### Command Injection

Look for combinations such as:

```text
command separator
        +
OS command
        +
suspicious user-controlled parameter
```

For example:

```text
host=127.0.0.1%3Bwhoami
```

---

## SOC L1 Key Takeaways

* Command injection involves suspicious OS-command input being supplied to an application.
* Look for suspicious shell metacharacters and command names.
* URL encoding can hide suspicious characters.
* Always consider decoding/normalization during investigation.
* A suspicious HTTP request proves an **attempt**, not necessarily successful execution.
* Check web logs together with application, system, and process telemetry.
* `404` means **Not Found**; it does not automatically mean the request was harmless.
* L1 analysts should **detect, analyze, document, and escalate** rather than perform advanced exploitation.
