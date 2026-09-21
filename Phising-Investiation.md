# Phishing Investigation — SOC Notes

## 1. What is Phishing?

**Phishing** = an attacker sends a fake message pretending to be a trusted person/company to trick someone into:

- Clicking a malicious link
- Opening a malicious attachment
- Giving a password
- Giving sensitive information
- Sending money

### Common signs

- Urgent/threatening language
- Suspicious sender/domain
- Unexpected attachment
- Suspicious URL
- Credential/login request
- Lookalike domain
- Authentication failures

---

# 2. Attachments

Attachments can contain malware or redirect the victim to a malicious website.

### Suspicious extensions

```text
.exe
.scr
.bat
.cmd
.ps1
.docm
.xlsm
.html
.zip
.rar
.7z
```

### Double extensions

```text
Salary_Update.pdf.exe
Invoice.docx.exe
```

The **real extension is the last one**:

```text
Salary_Update.pdf.exe
                ↑
              .exe
```

### Important

A suspicious extension **doesn't automatically prove malware**.

Investigate:

- Who sent it?
- Was it expected?
- What is the real file type?
- Was it opened?
- Has it been seen before?
- Check the file hash if available.

---

# 3. Sender and Domain

### Sender

```text
security@microsoft-alerts.com
```

The sender claims to be:

```text
security@microsoft-alerts.com
```

### Domain

Everything after `@`:

```text
security@microsoft-alerts.com
        ↓
microsoft-alerts.com
```

Do **not** confuse:

```text
@microsoft-alerts.com
```

with the domain itself.

---

# 4. Lookalike Domains

Attackers may create domains that look legitimate.

```text
paypa1.com
micros0ft.com
secure-paypal.com
microsoft-alerts.com
```

These are not the same as:

```text
paypal.com
microsoft.com
```

### Important rule

The actual domain is the important part at the right side of the hostname.

Example:

```text
login.microsoft.com.attacker.com
```

Actual domain:

```text
attacker.com
```

Not:

```text
microsoft.com
```

---

# 5. URL Anatomy

Example:

```text
https://secure-login.paypal.com/account/verify?user=sara&id=92
```

### Parts

```text
https://
```

Protocol

```text
secure-login.paypal.com
```

Hostname

```text
secure-login
```

Subdomain

```text
paypal.com
```

Actual domain

```text
/account/verify
```

Path

```text
user=sara&id=92
```

Parameters/query

### Important

HTTPS does **not** automatically mean the website is legitimate.

---

# 6. URL Tricks

### Fake-looking subdomain

```text
accounts.google.com.security-check.net
```

Actual domain:

```text
security-check.net
```

### Legitimate domain with suspicious parameter

```text
paypal.com/login?redirect=evil.com
```

Actual domain:

```text
paypal.com
```

`evil.com` is only inside a parameter.

Still investigate the parameter because it may be relevant.

---

# 7. Email Headers

Email headers contain technical information about an email.

Think of them like a **delivery record for a parcel**.

### Important headers

```text
From
To
Return-Path
Received
```

---

## From

```text
From: CEO@company.com
```

Who the email **claims** to be from.

---

## To

```text
To: finance@company.com
```

Who received the email.

---

## Return-Path

```text
Return-Path: random123@evil.com
```

The address used for handling **bounces/failed delivery**.

A mismatch with `From` can be suspicious.

### Important

A different Return-Path does **not automatically prove phishing**.

Legitimate email services and mailing systems can have different addresses.

---

## Received

Example:

```text
Received:
evil.com → mail.company.com → user
```

Shows the servers/hops involved in delivering the message.

It helps investigators understand the email's path.

---

# 8. Header Investigation Example

```text
From: CEO@company.com
To: finance@company.com
Return-Path: random123@evil.com

Received:
evil.com → company.com
```

Observations:

```text
From = CEO@company.com
To = finance@company.com
Return-Path = random123@evil.com
```

The Return-Path mismatch is a **clue**.

The Received information shows the message came through infrastructure associated with `evil.com`.

Do not automatically conclude:

> "The real sender is evil.com."

Continue investigating.

---

# 9. SPF

## What is SPF?

**SPF = Sender Policy Framework**

Simple meaning:

> **Is this sending server/IP authorized to send email for the domain?**

Example:

```text
From: support@company.com

Sending IP:
10.10.10.20
```

SPF record:

```text
v=spf1 ip4:10.10.10.20 -all
```

The IP matches.

```text
SPF = PASS ✅
```

---

## SPF FAIL

SPF record:

```text
v=spf1 ip4:10.10.10.20 -all
```

Sending IP:

```text
10.10.10.50
```

The IP is not authorized.

```text
SPF = FAIL ❌
```

Correct SOC wording:

> The sending IP is not authorized by the domain's SPF policy.

Do **not** say:

> The server is definitely hacked/insecure.

---

# 10. SPF Results

```text
PASS
```

Sending IP is authorized.

```text
FAIL
```

Sending IP is not authorized.

```text
SOFTFAIL
```

Probably unauthorized, but the policy does not request a hard failure/rejection.

### Remember

```text
PASS      → authorized
FAIL      → unauthorized
SOFTFAIL  → probably unauthorized
```

SPF failure is **evidence**, not automatic proof of phishing.

---

# 11. SPF DNS Record

SPF is published in DNS as a TXT record.

Check with:

```bash
dig TXT example.com
```

or:

```bash
nslookup -type=TXT example.com
```

Look for:

```text
v=spf1
```

---

# 12. SPF `ip4:`

Example:

```text
v=spf1 ip4:10.10.10.20 -all
```

Means:

> `10.10.10.20` is authorized to send.

---

# 13. SPF `include:`

Example:

```text
v=spf1 include:mail.example.com -all
```

`include:` means:

> **Check the other domain's SPF rules too.**

Example:

```text
company.com
     ↓
include:mail.example.com
     ↓
Check mail.example.com's SPF
     ↓
Find authorized servers
```

It does **not** mean every server belonging to that company is automatically trusted.

---

# 14. SPF `-all`

Example:

```text
v=spf1 ip4:10.10.10.20 -all
```

`-all` means:

> Other sending sources should fail SPF.

---

# 15. DKIM

**DKIM = DomainKeys Identified Mail**

Simple meaning:

> **DKIM uses a digital signature to help verify the email.**

Think of it like a digital seal.

```text
Private key
     ↓
Creates signature
     ↓
Email
     ↓
Public key
     ↓
Verifies signature
```

### Important

The public key does **not** verify the private key.

Correct:

```text
Private key → creates signature
Public key  → verifies signature
```

The private key stays secret.

---

# 16. DKIM PASS / FAIL

```text
DKIM: PASS
```

The DKIM signature successfully verified.

```text
DKIM: FAIL
```

The signature could not be successfully verified.

Possible reasons include:

- Message modification
- Invalid signature
- Incorrect configuration
- Other verification problems

DKIM FAIL alone does **not** automatically prove phishing.

---

# 17. DKIM `d=`

Example:

```text
DKIM-Signature:
d=company.com
```

`d=` identifies the **signing domain**.

Remember:

```text
d = domain
```

---

# 18. DKIM `s=`

Example:

```text
DKIM-Signature:
d=company.com;
s=google;
```

`s=` is the **selector**.

It tells the receiving server which DKIM public key to look for.

Conceptually:

```text
google._domainkey.company.com
```

Remember:

```text
d = WHO signed?
s = WHICH key should I look for?
```

---

# 19. DMARC

**DMARC = Domain-based Message Authentication, Reporting & Conformance**

Simple meaning:

> **Does the authentication match the domain shown in the From address?**

### SPF asks:

> Is the sending server authorized?

### DKIM asks:

> Is the signature valid?

### DMARC asks:

> Does the authentication match the visible From domain?

---

# 20. DMARC Alignment

Example:

```text
From: support@company.com

SPF: PASS
Authenticated domain: company.com
```

Domains match:

```text
company.com = company.com
```

SPF is aligned.

DMARC can pass based on this authentication.

---

### Different domain

```text
From: support@company.com

SPF: PASS
Authenticated domain: evil.com
```

SPF can still PASS because the sending server may be authorized for `evil.com`.

But:

```text
company.com ≠ evil.com
```

So SPF is **not aligned** with the visible From domain.

If there is no valid aligned DKIM authentication either:

```text
DMARC = FAIL ❌
```

### Critical concept

```text
SPF PASS ≠ automatically DMARC PASS
```

---

# 21. DMARC Does Not Require Both SPF and DKIM

Generally, DMARC can pass if **at least one** of these is valid and aligned:

```text
SPF PASS + aligned
        OR
DKIM PASS + aligned
```

Example:

```text
SPF: FAIL
DKIM: PASS + aligned
DMARC: PASS
```

Another:

```text
SPF: PASS + aligned
DKIM: FAIL
DMARC: PASS
```

But:

```text
SPF: PASS but NOT aligned
DKIM: FAIL
DMARC: FAIL
```

---

# 22. DMARC Policies

The domain owner publishes a DMARC policy in DNS.

### `p=none`

```text
v=DMARC1; p=none
```

Meaning:

> Monitor/report; don't request strong enforcement.

Think:

```text
none → Watch 👀
```

---

### `p=quarantine`

```text
v=DMARC1; p=quarantine
```

Meaning:

> Treat failing messages as suspicious.

Often:

```text
Spam / quarantine
```

Think:

```text
quarantine → Suspicious ⚠️
```

---

### `p=reject`

```text
v=DMARC1; p=reject
```

Meaning:

> Request rejection of messages that fail DMARC.

Think:

```text
reject → Block ❌
```

---

# 23. Who Creates DMARC Policies?

The **domain owner/organization** does.

Example:

```text
Company/security team
        ↓
Creates DMARC policy
        ↓
Publishes it in DNS
        ↓
Receiving mail server reads it
        ↓
Applies the policy
```

The receiving mail server doesn't invent the domain's policy.

---

# 24. DMARC Is Not a Complete Phishing Detector

A message can have:

```text
SPF: PASS
DKIM: PASS
DMARC: PASS
```

and still be phishing.

Example:

```text
From: security@fake-paypal.com
```

The attacker may properly configure SPF/DKIM/DMARC for their **own fake domain**.

Therefore:

> Authentication results are evidence, not the entire phishing decision.

---

# 25. `Authentication-Results`

You may see something like:

```text
Authentication-Results:
spf=pass
dkim=fail
dmarc=fail
```

This gives you the authentication results for that email.

### SOC interpretation

```text
SPF = PASS
DKIM = FAIL
DMARC = FAIL
```

Do not immediately write:

> "Definitely phishing."

Instead:

> SPF passed, but DKIM and DMARC failed. Further investigation is required.

---

# 26. Full Phishing Investigation Flow

When investigating an email:

```text
1. Sender
      ↓
2. Recipient
      ↓
3. Subject/body
      ↓
4. Attachments
      ↓
5. Sender domain
      ↓
6. URLs
      ↓
7. Email headers
      ↓
8. SPF
      ↓
9. DKIM
      ↓
10. DMARC
      ↓
11. Combine all evidence
      ↓
12. Make an investigation assessment
```

---

# 27. Example Investigation

### Email

```text
From:
security@microsoft-alerts.com

To:
sara@company.com

Subject:
URGENT: Your account will be disabled
```

Body:

```text
Your account will be disabled within 2 hours.
Verify your account immediately.
```

URL:

```text
https://login.microsoft.com.security-check.net/verify?user=sara
```

### Investigation

**Sender domain:**

```text
microsoft-alerts.com
```

Suspicious because it is not:

```text
microsoft.com
```

**URL hostname:**

```text
login.microsoft.com.security-check.net
```

**Actual domain:**

```text
security-check.net
```

**Urgency:**

```text
"disabled within 2 hours"
```

Pressure tactic.

**Credential request:**

User is being asked to verify an account.

### Assessment

Multiple independent indicators make the email **likely phishing**, while the analyst can continue checking headers, authentication results, URLs, attachments, and other evidence.

---

# 28. SOC Language

Avoid:

```text
"The email is definitely phishing because SPF failed."
```

Better:

```text
"SPF failed, indicating that the sending IP was not authorized by the domain's SPF policy."
```

Then combine evidence:

```text
"SPF failed, the sender domain does not match the claimed organization, and the URL uses a lookalike hostname. These indicators warrant further investigation."
```

---

# 29. Quick Cheat Sheet

```text
PHISHING
↓
Fake message designed to trick the victim.

ATTACHMENT
↓
Check extension, double extensions, source, hash, whether expected.

DOMAIN
↓
Check the actual domain, not just the visible name.

URL
↓
Check hostname, actual domain, path, parameters.

FROM
↓
Who the email claims to be from.

TO
↓
Who received it.

RETURN-PATH
↓
Where bounce/failed-delivery mail goes.

RECEIVED
↓
Email delivery path/hops.

SPF
↓
Is the sending IP/server authorized?

DKIM
↓
Is the digital signature valid?

DMARC
↓
Does authentication align with the From domain?

DMARC POLICY
↓
none = monitor
quarantine = treat as suspicious
reject = reject

FINAL SOC DECISION
↓
Never rely on one indicator.
Combine multiple pieces of evidence.
```

---

# 30. Core Things to Memorize

```text
SPF  = Authorized server?
DKIM = Valid signature?
DMARC = Does authentication match From?

d=   → signing domain
s=   → DKIM selector

ip4: → authorize an IP
include: → use another domain's SPF rules
-all → other sources fail

p=none       → monitor
p=quarantine → suspicious/quarantine
p=reject     → reject
```
