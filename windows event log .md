# Windows Event Logs — SOC L1

> **Core idea:** Windows Event Logs are records of activity on a Windows system. A SOC analyst uses them to reconstruct **who did what, when, where, and what happened next**.

---

## 1. The Mental Model

Think of Windows Event Logs as **CCTV for a Windows machine**.

```text
Something happens
      ↓
Windows generates an event
      ↓
Event is stored in a log
      ↓
SOC investigates + correlates events
```

### Every event →

```text
WHO + WHAT + WHEN + WHERE
```

### Investigation →

```text
EVENT + CONTEXT + CORRELATION + IMPACT
```

> **One event = clue. Correlated events = evidence.**

---

# 2. Main Windows Logs

| Log             | What it tells you                           | SOC relevance |
| --------------- | ------------------------------------------- | ------------- |
| **Security**    | Authentication, accounts, security activity | ⭐⭐⭐           |
| **System**      | Services, drivers, Windows components       | ⭐⭐            |
| **Application** | Application/software events                 | ⭐⭐            |

Start with:

```text
Security → authentication/security
System → Windows/services
Application → software
```

---

# 3. Event ID

An **Event ID** identifies the type of event.

Example:

```text
4624 → Successful logon
4625 → Failed logon
4688 → Process creation
```

Think:

```text
Event ID = "What type of thing happened?"
```

Don't memorize hundreds of IDs. Learn the important ones and know how to look up unfamiliar IDs.

---

# 4. High-Value Event IDs

| Event ID | Meaning                                        | Investigation question                          |
| -------- | ---------------------------------------------- | ----------------------------------------------- |
| **4624** | Successful logon                               | Who successfully logged in?                     |
| **4625** | Failed logon                                   | Who/where are failed attempts coming from?      |
| **4688** | Process creation                               | What process started and why?                   |
| **4720** | User account created                           | Who created the account?                        |
| **4722** | User account enabled                           | Who enabled it?                                 |
| **4725** | User account disabled                          | Who disabled it?                                |
| **4726** | User account deleted                           | Who deleted it?                                 |
| **4732** | Member added to a security-enabled local group | Was privilege/access changed?                   |
| **7045** | New service installed                          | Was a service installed for legitimate reasons? |
| **1102** | Security audit log cleared                     | Who cleared the log and why?                    |

> **Important:** Event IDs and the exact telemetry available depend on Windows auditing/configuration.

---

# 5. Authentication Investigation

### 4624 — Successful Logon

Example:

```text
User: Ahmed
Event ID: 4624
Time: 10:32
Source IP: 192.168.1.20
Logon Type: 10
```

Ask:

```text
Who?
When?
From where?
What logon type?
Was it expected?
What happened afterward?
```

### 4625 — Failed Logon

Example:

```text
10:01 → Failed
10:01 → Failed
10:02 → Failed
10:02 → Failed
10:03 → Successful
```

This can be suspicious, but **failed logons alone do not prove brute force**.

Investigate:

* Number of attempts
* Time period
* Account
* Source IP
* Destination host
* Whether a successful login followed
* Whether the behavior is normal for that account

---

# 6. Important Logon Types

| Type   | Meaning                                |
| ------ | -------------------------------------- |
| **2**  | Interactive/local logon                |
| **3**  | Network logon                          |
| **10** | Remote interactive logon, commonly RDP |

Example:

```text
4624
+
Logon Type 10
+
Unusual source IP
```

→ Investigate the remote login.

---

# 7. Process Creation — 4688

Event ID **4688** can record process creation when the relevant auditing is enabled.

Example:

```text
powershell.exe
```

Ask:

```text
Who started it?
What is the parent process?
What command line was used?
Where is the executable/script?
What happened after it started?
```

### Process tree

```text
WINWORD.EXE
     ↓
powershell.exe
     ↓
script.ps1
```

This is more informative than simply seeing:

```text
powershell.exe
```

### Key idea

**Parent process = what launched this process?**

---

# 8. PowerShell Investigation

Example:

```powershell
powershell.exe -ExecutionPolicy Bypass -File C:\Users\Ahmed\Downloads\update.ps1
```

Meaning:

```text
PowerShell
   ↓
bypass normal execution-policy restrictions
   ↓
execute a PowerShell script
```

`ExecutionPolicy Bypass` is **suspicious but not proof of malicious activity**.

Investigate:

* Parent process
* Script path
* Script contents
* Command line
* File origin
* File hash
* Network connections
* Child processes
* User/account

---

# 9. Account Events

Account activity can reveal:

```text
Account created
Account enabled
Account disabled
Account deleted
Group membership changed
```

Example:

```text
4720
↓
New account created
↓
4732
↓
Account added to security-enabled group
```

Ask:

> Was this an authorized administrative action?

---

# 10. Service Installation — 7045

A new Windows service can be legitimate or suspicious.

Example investigation:

```text
7045
↓
New service installed
↓
What executable does it launch?
↓
Who installed it?
↓
When?
↓
Was software being legitimately installed?
```

A service installation combined with suspicious execution or persistence indicators deserves investigation.

---

# 11. Security Log Cleared — 1102

Event ID **1102** indicates the Security audit log was cleared.

Example:

```text
Suspicious activity
      ↓
Security log cleared
```

This deserves investigation because removing logs can destroy evidence.

But:

> **Log clearing ≠ automatically malicious.**

Check:

* User
* Time
* Host
* Administrative activity
* Change ticket/maintenance
* Events before the clearing

---

# 12. Event Viewer

Open Windows Event Viewer:

```text
eventvwr.msc
```

Main area:

```text
Event Viewer
│
├── Windows Logs
│   ├── Application
│   ├── Security
│   ├── Setup
│   ├── System
│   └── Forwarded Events
│
└── Applications and Services Logs
```

For SOC fundamentals, prioritize:

```text
Security
System
Application
```

---

# 13. PowerShell — Get-WinEvent

Example:

```powershell
Get-WinEvent -LogName Security -MaxEvents 20
```

Meaning:

```text
Get-WinEvent
    ↓
Retrieve Windows events

-LogName Security
    ↓
From Security log

-MaxEvents 20
    ↓
Return up to 20 events
```

The command is useful for quickly inspecting events from the command line.

---

# 14. Don't Read Everything

Real systems can generate huge numbers of events.

Instead filter by:

```text
TIME
+
EVENT ID
+
USER
+
HOST
+
SOURCE IP
```

Example investigation question:

> "Show me authentication events for Ahmed around the time of the alert."

This is much more efficient than reading the entire Security log.

---

# 15. Event Correlation

This is the most important SOC skill.

### Example

```text
10:00 → 4625 Failed login
10:01 → 4625 Failed login
10:02 → 4624 Successful login
10:03 → 4688 PowerShell
10:04 → Suspicious process
10:05 → External network connection
```

Individually:

```text
Failed login      → normal possibility
Successful login  → normal possibility
PowerShell        → normal possibility
Network connection→ normal possibility
```

Together:

```text
Failed logins
     ↓
Successful login
     ↓
PowerShell
     ↓
Suspicious process
     ↓
External connection
```

Now you have a **potential attack chain**.

---

# 16. Timeline Thinking

Always reconstruct:

```text
BEFORE
   ↓
Initial access?
   ↓
DURING
   ↓
Execution?
   ↓
AFTER
   ↓
Persistence?
   ↓
Network activity?
   ↓
Impact?
```

Example:

```text
09:58  Phishing email received
10:01  User clicked link
10:02  File downloaded
10:03  PowerShell started
10:04  New process created
10:05  External connection
```

The timeline connects separate telemetry sources.

---

# 17. Event Logs Are Not Perfect

Never assume:

> "If there is no event, it didn't happen."

Possible reasons:

* Auditing wasn't enabled
* Logs were overwritten
* Logs were cleared
* The activity was recorded elsewhere
* Endpoint/EDR telemetry has the better evidence
* Different Windows configurations generate different events

Therefore:

> **Event Logs are evidence, not a perfect recording of everything that happened.**

---

# 18. L1 Investigation Workflow

```text
ALERT
 ↓
Identify affected host/user
 ↓
Set investigation time window
 ↓
Check Security events
 ↓
Check authentication
 ↓
Check process creation
 ↓
Check System events
 ↓
Check Application events
 ↓
Build timeline
 ↓
Correlate related events
 ↓
Determine legitimate vs suspicious
 ↓
Assess impact
 ↓
Document
 ↓
Escalate according to IR procedure
```

---

# 19. Five Questions to Ask

For **every suspicious event**:

### WHO?

User/account/process?

### WHAT?

What happened?

### WHEN?

Exact timestamp?

### WHERE?

Host/IP/location/resource?

### WHAT NEXT?

What happened immediately afterward?

That last question is often the most important.

---

# 20. Common SOC Mistakes

### ❌ "4625 means attack."

No.

It means a failed logon occurred.

### ❌ "PowerShell means malware."

No.

PowerShell is legitimate Windows software.

### ❌ "7045 means persistence."

No.

It means a service was installed.

### ❌ "1102 means attacker."

No.

It means the Security audit log was cleared.

### ❌ "No log = no activity."

Incorrect.

Logging/auditing may be incomplete.

### ✅ Correct approach

```text
Event
 ↓
Context
 ↓
Correlation
 ↓
Investigation
 ↓
Conclusion
```

---

# 21. High-Value SOC Pattern

Remember:

```text
AUTHENTICATION
      ↓
EXECUTION
      ↓
PERSISTENCE
      ↓
NETWORK ACTIVITY
      ↓
IMPACT
```

Windows Event Logs can provide evidence at several points in this chain.

---

# 22. Quick Reference

```text
4624 → Successful logon
4625 → Failed logon
4688 → Process created
4720 → Account created
4722 → Account enabled
4725 → Account disabled
4726 → Account deleted
4732 → Added to security-enabled local group
7045 → Service installed
1102 → Security log cleared
```

---

# 23. The Career-Level Mental Model

Don't memorize:

> "4688 = process creation."

Think:

```text
4688
 ↓
What process?
 ↓
Who launched it?
 ↓
Parent process?
 ↓
Command line?
 ↓
File location?
 ↓
What happened next?
```

That's the difference between **knowing Event IDs** and **using Event Logs as a SOC analyst**.

---

# Final Memory Formula

## EVENT

**WHO + WHAT + WHEN + WHERE**

## INVESTIGATION

**EVENT + CONTEXT + CORRELATION + IMPACT**

## SOC THINKING

**Don't investigate isolated events. Reconstruct the story.**

> **One event is a clue. A correlated sequence is evidence.**
