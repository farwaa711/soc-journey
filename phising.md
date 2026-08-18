# Phishing Detection & Investigation — SOC L1

> **Core idea:** Phishing is a social-engineering attack where an attacker tricks a user into clicking a link, opening a file, revealing credentials, or performing an unsafe action.

---

## 1. The SOC Mental Model

Remember the investigation as:

```text
EMAIL
  ↓
LINK / ATTACHMENT
  ↓
DNS
  ↓
WEB REQUEST
  ↓
ENDPOINT ACTIVITY
  ↓
ACCOUNT ACTIVITY
  ↓
IMPACT
```

Your job is to **connect these events into one timeline**.

---

# 2. Phishing Red Flags

### Email

Look for:

* Suspicious sender/domain
* Look-alike domain
* Unexpected email
* Urgent/threatening language
* Request for credentials/payment
* Unexpected attachment
* Suspicious link
* Mismatched displayed URL vs actual URL
* Unusual reply-to address

### Example

```text
From: microsoft-support@micr0soft-example.com
Link: https://micr0soft-example.com/login
```

The domain deserves investigation.

> **Important:** A suspicious-looking email is an indicator, not proof by itself.

---

# 3. Domain Investigation

Check:

* Domain spelling
* Domain age/reputation
* DNS records
* IP address
* Hosting/provider
* Whether the domain resembles a legitimate organization

### DNS evidence

If Ahmed clicks:

```text
https://fake-login.example/login
```

DNS logs may show:

```text
Ahmed's device
      ↓
DNS query
      ↓
fake-login.example
      ↓
IP address
```

**DNS tells you that the device resolved the domain.**

It does NOT prove that the user successfully visited the website.

---

# 4. Proxy / Web Logs

Proxy/web logs can show:

```text
User/device
Destination domain
URL
HTTP method
Status code
Timestamp
User-Agent
```

Example:

```text
Ahmed
→ fake-login.example/login
→ GET
→ 200
```

This provides stronger evidence that the device **made a web request**.

---

# 5. Endpoint Logs

Endpoint telemetry tells you what happened **on the user's machine**.

Look for:

* Browser process
* File download
* File creation
* Script execution
* PowerShell
* CMD
* Office spawning unusual processes
* New processes
* Persistence
* Credential-access behavior

Example:

```text
WINWORD.EXE
      ↓
powershell.exe
      ↓
script.ps1
```

This is much more concerning than simply opening a webpage.

---

# 6. The Critical Correlation

Suppose you see:

```text
10:01  Ahmed receives suspicious email
10:03  Ahmed's device queries phishing-domain.com
10:03  Browser connects to phishing-domain.com
10:04  file.exe downloaded
10:04  powershell.exe starts
10:05  suspicious script executes
```

Now you have a **connected attack chain**.

Do not investigate each log independently.

**Correlate them by:**

* User
* Host
* IP
* Domain
* Timestamp
* Process
* File
* URL

---

# 7. Attachments

Attachments can be:

* `.exe`
* `.js`
* `.vbs`
* `.ps1`
* `.bat`
* `.cmd`
* Malicious Office documents
* PDFs containing malicious links
* Archives such as `.zip`

### Important question

> **Was the attachment merely received, or was it actually opened/executed?**

These are very different situations.

---

# 8. PowerShell = Investigate Further

Example:

```text
powershell.exe -ExecutionPolicy Bypass -File C:\Users\Ahmed\Downloads\update.ps1
```

Meaning:

```text
PowerShell
→ bypass normal execution-policy restrictions
→ execute a PowerShell script
```

`ExecutionPolicy Bypass` is **suspicious but not proof of malware**.

Investigate:

* Parent process
* Script path
* Script contents
* File hash
* Network connections
* Child processes
* User who launched it
* Whether it came from email/browser download

---

# 9. Credential Phishing

A phishing page may imitate:

* Microsoft login
* Google login
* Banking portal
* Company VPN
* Email portal

The key question is:

> **Did the user only visit the page, or did they submit credentials?**

Investigate:

```text
Phishing URL
      ↓
Login page visited
      ↓
POST / login request?
      ↓
Authentication logs
      ↓
Successful login?
      ↓
Unusual location/device/IP?
```

---

# 10. Authentication Evidence

If credentials were submitted, check:

* Successful login
* Failed login attempts
* Source IP
* Geographic location
* Device
* MFA events
* New session
* Impossible/unusual travel
* Password reset
* Account changes

### Example

```text
09:10  Ahmed visits phishing login page
09:12  Credentials submitted
09:13  Successful login from unusual IP
09:14  MFA event
09:16  New mailbox rule created
```

This could indicate **account compromise**, not merely phishing exposure.

---

# 11. What Each Log Tells You

| Evidence               | What it answers                                  |
| ---------------------- | ------------------------------------------------ |
| **Email log**          | What was sent to the user?                       |
| **DNS log**            | What domain did the device resolve?              |
| **Proxy/web log**      | What web destination/request occurred?           |
| **Endpoint log**       | What happened on the machine?                    |
| **Process telemetry**  | What executed and who/what launched it?          |
| **File telemetry**     | What was downloaded/created?                     |
| **Authentication log** | Was an account accessed?                         |
| **EDR alert**          | Did security controls detect malicious behavior? |

---

# 12. Investigation Questions

When you receive a phishing alert, ask:

### Email

```text
Who sent it?
Who received it?
When?
What was the subject?
Was there an attachment?
Was there a link?
```

### Domain

```text
What domain?
Is it legitimate?
What IP did it resolve to?
Who else queried it?
```

### Web

```text
Did the user visit it?
What URL?
When?
What response?
Was anything downloaded?
```

### Endpoint

```text
Was a file created?
Was it opened?
What process executed?
Was PowerShell involved?
What child processes appeared?
```

### Account

```text
Were credentials submitted?
Was there a successful login?
Was MFA triggered?
Were account settings changed?
```

### Scope

```text
Who else received the email?
Who else clicked?
Who else downloaded the file?
Who else contacted the domain?
```

---

# 13. Scope Is Critical

Finding one phishing email does **not** mean only one person was targeted.

Search for:

```text
Same sender
Same subject
Same domain
Same URL
Same attachment
Same file hash
Same IP
Same campaign
```

You may discover multiple affected users.

---

# 14. Severity Thinking

### Low

```text
Phishing email received
+
User did not click
+
No further activity
```

### Medium

```text
Email
+
User clicked
+
Phishing site visited
```

### High

```text
Email
+
User clicked
+
Credentials submitted
```

or:

```text
Email
+
Attachment executed
+
Suspicious process activity
```

### Critical potential

```text
Credential submission
+
Successful suspicious authentication
+
Account changes/data access
```

or:

```text
Malicious attachment
+
Execution
+
Persistence / lateral movement / data theft indicators
```

Actual severity depends on your organization's incident-response policy.

---

# 15. False Positives

Do not automatically classify something as phishing because:

* Domain looks unusual
* Email contains urgency
* Link is unfamiliar
* User-Agent looks strange

Legitimate possibilities include:

* Marketing emails
* Vendor notifications
* Security testing
* Password-reset systems
* Newly registered legitimate domains

**Always investigate context.**

---

# 16. Evidence Timeline

Build a simple timeline:

```text
TIME        EVENT
10:01       Email received
10:03       DNS lookup
10:03       Website visited
10:04       File downloaded
10:04       PowerShell started
10:05       Script executed
10:06       External connection
10:08       Suspicious authentication
```

This timeline often turns separate logs into one understandable incident.

---

# 17. L1 Phishing Workflow

```text
ALERT
 ↓
Inspect email
 ↓
Analyze sender/domain
 ↓
Extract URL/attachment
 ↓
Check DNS
 ↓
Check proxy/web logs
 ↓
Check endpoint telemetry
 ↓
Check process execution
 ↓
Check authentication
 ↓
Determine impact
 ↓
Search for other affected users
 ↓
Document timeline
 ↓
Escalate according to IR playbook
```

---

# 18. The Most Important Distinction

Remember:

```text
RECEIVED ≠ CLICKED

CLICKED ≠ DOWNLOADED

DOWNLOADED ≠ EXECUTED

EXECUTED ≠ COMPROMISED

COMPROMISED ≠ DATA THEFT
```

Each step requires **evidence**.

This prevents overreacting to weak indicators.

---

# 19. Your SOC Investigation Formula

## **WHO → WHAT → WHEN → WHERE → ACTION → IMPACT**

**WHO**
Which user/device?

**WHAT**
Email, URL, attachment, process?

**WHEN**
Exact timestamps?

**WHERE**
Domain/IP/endpoint?

**ACTION**
Clicked? Downloaded? Executed? Logged in?

**IMPACT**
Credential compromise? Malware? Account takeover? Data access?

---

# 20. Memory Example

If you see:

```text
Ahmed receives email
        ↓
Clicks suspicious link
        ↓
DNS resolves phishing domain
        ↓
Browser visits page
        ↓
File downloaded
        ↓
PowerShell executes
```

Think:

> **Email → DNS → Web → Endpoint → Process → Impact**

That chain is the heart of a phishing investigation.

---

# Quick L1 Checklist

* [ ] Identify sender
* [ ] Identify recipients
* [ ] Check sender/domain
* [ ] Extract URL
* [ ] Check attachment
* [ ] Check DNS activity
* [ ] Check web/proxy activity
* [ ] Determine whether user clicked
* [ ] Determine whether file was downloaded
* [ ] Determine whether file was executed
* [ ] Check PowerShell/CMD/process activity
* [ ] Check authentication activity
* [ ] Check account changes
* [ ] Search for other affected users
* [ ] Build timeline
* [ ] Assess impact
* [ ] Document and escalate

---

# One-Line Career Reminder

> **A SOC analyst does not stop at "this email is phishing." The real investigation is: Did the user interact with it, what happened afterward, and what evidence proves the impact?**
