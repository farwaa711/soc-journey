# 🛡️ VirusTotal + MITRE ATT&CK — SOC Notes

## 1. VirusTotal

### What is VirusTotal?

VirusTotal is a **threat intelligence platform** used to investigate:

* Files
* Hashes
* IP addresses
* Domains
* URLs

### SOC Workflow

```text
Suspicious File
      ↓
Get SHA-256
      ↓
Search VirusTotal
      ↓
Check Detections
      ↓
Check Behavior
      ↓
Check Relations
      ↓
Investigate & Correlate
```

---

## 2. Hash

A hash is a **unique fingerprint of a file**.

Common hashes:

```text
MD5
SHA-1
SHA-256
```

For SOC work, **SHA-256** is commonly used.

### Example

```text
File → SHA-256 → Search VirusTotal
```

### Important

A hash identifies a specific file/version.

```text
Hash ≠ proof that a file is malicious
```

---

# 🔍 3. VirusTotal Detection Ratio

Example:

```text
65 / 68
```

Means:

> 65 out of 68 security engines detected/flagged the file.

### Important

```text
1/68  → Not automatically safe
65/68 → Strong detection signal
```

Always consider the context.

---

# 🏷️ 4. Vendors & Classifications

### Vendors

Security companies/engines that analyze the file.

Example:

```text
AhnLab
Alibaba
Microsoft
```

### Classification

The name/label a vendor gives its detection.

Different vendors can use different names for the same sample.

---

# 📄 5. File Type

Shows what type of file VirusTotal identifies.

Example:

```text
PowerShell
PE32
PDF
```

---

# 🔮 6. Magic

**Magic** identifies a file using its internal file signature/content rather than trusting the filename extension.

Example:

```text
invoice.pdf
      ↓
Internal content = executable
```

The `.pdf` extension alone does not prove it is actually a PDF.

---

# 🤖 7. Magika

**Magika** is Google's AI-based file-type identification system.

```text
Magic  → File signature/content
Magika → AI-based file type identification
```

They can sometimes give different results because they use different methods.

---

# 🧪 8. Behavior

### Definition

> **Behavior = what a file/program does when it is executed.**

VirusTotal can use sandboxes to observe these actions.

### Main Behavior Areas

```text
Process Activity
Network Activity
File Activity
Registry Activity
Persistence
Defense Evasion
```

### Example

```text
File
 ↓
Runs PowerShell
 ↓
Creates a file
 ↓
Contacts an IP
```

These are **behaviors**.

---

# 🧪 9. Sandbox

A sandbox is a **controlled, isolated environment** used to safely run and observe a suspicious file.

```text
Suspicious File
      ↓
Sandbox
      ↓
Execute
      ↓
Observe behavior
```

Different sandboxes/environments can produce different behavior results.

So don't assume every line on a multi-sandbox report happened on one computer.

---

# 🌐 10. Network Behavior

VirusTotal may show:

### DNS

```text
Domain → IP
```

Example:

```text
api.example.com
```

### IP Traffic

Shows IP addresses the sample communicated with.

### Port

Example:

```text
443 → commonly HTTPS/TLS
```

### Important

```text
Contacted IP/domain ≠ automatically malicious
```

Investigate the context and reputation.

---

# 📁 11. File Activity

Behavior may show:

### Created/Written

A program creates or writes a file.

### Deleted

A program removes a file.

### Dropped File

> A file created/written by a program during execution.

Example:

```text
sample
  ↓
creates
  ↓
script.ps1
```

---

# 🌳 12. Process Tree

A process tree shows **parent → child** relationships.

Example:

```text
Word
 ↓
PowerShell
 ↓
cmd
```

Meaning:

```text
Word started PowerShell
PowerShell started cmd
```

### Spawn

**Spawn = create/start another process.**

---

# ⚠️ 13. Suspended Process

A process can be created in a **suspended/paused state**.

```text
Create process
      ↓
⏸️ Paused
      ↓
Possible code manipulation/injection
      ↓
▶️ Run
```

This can be associated with **Process Injection**, depending on the observed behavior.

---

# 🔗 14. Relations / Pivoting

VirusTotal can show relationships between a sample and other indicators.

Examples:

```text
File
 ↓
Domains
 ↓
IP addresses
 ↓
Other files
```

### Pivoting

> Using one indicator to investigate related indicators.

Example:

```text
Suspicious Hash
      ↓
VirusTotal
      ↓
Related IP
      ↓
Investigate IP
```

---

# 🗺️ 15. MITRE ATT&CK

### What is MITRE ATT&CK?

MITRE ATT&CK is a framework that gives **standard names and IDs to attacker behaviors**.

```text
Behavior
   ↓
MITRE ATT&CK
   ↓
Technique / ID
```

### Important

MITRE labels **behaviors**, not entire attacks.

---

# 🎯 16. Tactic vs Technique vs Sub-technique

## Tactic = WHY / GOAL

What is the attacker trying to achieve?

Example:

```text
Execution
Persistence
Privilege Escalation
Defense Evasion
```

## Technique = HOW

How are they doing it?

Example:

```text
Process Injection
```

## Sub-technique = SPECIFIC HOW

A more specific version of a technique.

Example:

```text
Command & Scripting Interpreter
        ↓
PowerShell
```

---

# 🔢 17. Important MITRE Examples

| ID            | Meaning                         |
| ------------- | ------------------------------- |
| **T1059**     | Command & Scripting Interpreter |
| **T1059.001** | PowerShell                      |
| **T1055**     | Process Injection               |
| **T1547**     | Boot/Logon Autostart Execution  |
| **T1497**     | Virtualization/Sandbox Evasion  |

### Easy meanings

```text
T1059.001 → Uses PowerShell

T1055 → Puts code into another process

T1547 → Makes something start automatically

T1497 → Detects/tries to avoid sandbox/virtualization
```

---

# 🔥 18. Behavior → MITRE

This is the most important connection.

```text
Program
   ↓
Does something
   ↓
Behavior
   ↓
MITRE maps the behavior
   ↓
Technique / Sub-technique
```

Example:

```text
PowerShell executes
       ↓
Command & Scripting Interpreter
       ↓
PowerShell
       ↓
T1059.001
```

---

# 🧠 19. SOC Analyst Thinking

Don't immediately say:

```text
MITRE technique = Malware
```

Instead:

```text
Observe
   ↓
Understand behavior
   ↓
Map to MITRE
   ↓
Check context
   ↓
Investigate
   ↓
Make conclusion
```

### Example

```text
Email attachment
      ↓
PowerShell starts
      ↓
PowerShell downloads script
```

Professional conclusion:

> **PowerShell execution and script download were observed. The activity is suspicious and requires further investigation.**

Don't automatically say:

> "The attachment is definitely malware."

---

# 🔐 20. Hash vs Behavior vs MITRE

| Concept          | Main Question                               |
| ---------------- | ------------------------------------------- |
| **Hash**         | Which file is this?                         |
| **VirusTotal**   | What is known/observed about it?            |
| **Behavior**     | What did it do?                             |
| **MITRE ATT&CK** | What known attacker behavior does it match? |

### Remember

```text
Hash → Identity
Behavior → Actions
MITRE → Behavior classification
```

---

# ⭐ Final SOC Mental Model

```text
Suspicious File / Alert
          ↓
       Hash
          ↓
     VirusTotal
          ↓
 ┌────────┴────────┐
 ↓                 ↓
Detections       Behavior
                   ↓
        Processes / Files /
        Network / Registry
                   ↓
             MITRE ATT&CK
                   ↓
       Tactic → Technique
          → Sub-technique
                   ↓
             Investigation
```

## 🔑 Golden Rules

* **Hash ≠ malware proof**
* **0 detections ≠ automatically safe**
* **Network connection ≠ automatically malicious**
* **PowerShell ≠ automatically malicious**
* **MITRE mapping ≠ automatically malicious**
* **Behavior is evidence, not an automatic verdict**
* **Context matters**
* **MITRE does not require a hash**
* **You do not need to map every single event to MITRE**

> **Behavior tells you WHAT happened.**
> **MITRE ATT&CK gives that behavior a standard name/ID.**
