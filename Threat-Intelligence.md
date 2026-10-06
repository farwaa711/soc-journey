# Day 23 — Threat Intelligence

## 1. What is Threat Intelligence?

**Threat Intelligence (TI)** = information about cyber threats that helps analysts **detect, investigate, and respond** to attacks.

```text
Alert → IOC → Enrichment → Intelligence → Decision
```

---

## 2. IOC Enrichment

**IOC (Indicator of Compromise)** = evidence that may indicate malicious activity.

Examples:

* IP address
* Domain
* URL
* File hash
* Email address
* Username

**IOC Enrichment** = adding context and information to an IOC.

### Example

```text
IOC:
185.199.108.153

Enrichment:
- Reputation: Malicious
- Associated domains: 5
- Related malware: RAT
- Previous reports: 30+
- Known C2 infrastructure
```

### Questions to ask

```text
Who owns it?
Is it malicious?
Has it been reported before?
What is it associated with?
Is it related to malware?
Has our environment communicated with it?
```

---

## 3. Threat Feeds

**Threat Feed** = regularly updated source of threat information.

Can contain:

```text
Malicious IPs
Malicious domains
Malicious URLs
Malware hashes
Phishing indicators
C2 infrastructure
```

### Example

```text
45.22.91.10 → Known C2
evil-login.com → Phishing
abc123... → Malware hash
```

⚠️ **Important:** A threat-feed match is a clue, not automatically proof of compromise.

---

## 4. Malware Intelligence

**Malware Intelligence** = information about malware, including its behavior and infrastructure.

Can tell you:

```text
Malware family
File hashes
C2 servers
Domains
Network behavior
Persistence methods
Capabilities
```

### Example

```text
Hash
 ↓
Malware identified as RAT
 ↓
Contacts C2 server
 ↓
Downloads files
 ↓
May steal credentials
```

---

## 5. Reputation Checking

**Reputation checking** = checking the history and trust level of an IOC.

Example:

```text
IP → 45.91.22.10

Reports: 82
Malware associations: 3
Related domains: 12

→ High-risk indicator
```

### Remember

```text
Reputation ≠ Final Verdict
```

Always combine reputation with:

* Internal logs
* User activity
* Host behavior
* Network activity
* Time/context

---

## 6. Threat Actors

**Threat Actor** = person or group responsible for malicious activity.

Examples:

* Cybercriminal groups
* Ransomware groups
* Nation-state groups
* Hacktivists
* Insider threats

Analysts may investigate:

```text
Who is behind the attack?
What malware do they use?
What infrastructure do they use?
Who do they target?
What TTPs do they use?
```

---

## 7. TTPs

**TTP = Tactics, Techniques, and Procedures**

### T — Tactics

**What is the attacker's goal?**

Examples:

```text
Initial Access
Execution
Persistence
Credential Access
Discovery
Command & Control
Exfiltration
```

### T — Techniques

**How does the attacker achieve the goal?**

Example:

```text
Tactic: Execution
Technique: PowerShell
```

### P — Procedures

**Exactly how was the technique performed?**

Example:

```powershell
powershell.exe -ExecutionPolicy Bypass
```

### Easy memory

```text
TACTIC     = WHY / GOAL
TECHNIQUE  = HOW
PROCEDURE  = EXACT METHOD
```

---

# 8. Threat Intelligence Investigation Flow

```text
Alert
  ↓
Extract IOC
  ↓
Enrich IOC
  ↓
Check Reputation
  ↓
Check Threat Feeds
  ↓
Check Malware Intelligence
  ↓
Identify Threat Actor / TTPs
  ↓
Search Internal Logs
  ↓
Assess Risk
  ↓
Respond / Escalate
```

---

# 9. Example SOC Investigation

### Alert

```text
PowerShell downloaded a file
from evil-update.com
```

### Investigation

```text
IOC:
evil-update.com
        ↓
Threat Feed:
Known malicious domain
        ↓
File Hash:
Associated with RAT
        ↓
Malware Intelligence:
RAT communicates with C2
        ↓
TTP:
PowerShell execution
        ↓
Internal Search:
Check other hosts/users
        ↓
Verdict:
Suspicious / Malicious
```

---

# 10. Key Difference

| Term                     | Meaning                                                |
| ------------------------ | ------------------------------------------------------ |
| **IOC**                  | The suspicious clue                                    |
| **IOC Enrichment**       | Adding context to the clue                             |
| **Threat Feed**          | Source of threat indicators                            |
| **Malware Intelligence** | Information about malware                              |
| **Reputation**           | History/trustworthiness of an indicator                |
| **Threat Actor**         | Who is behind the attack                               |
| **TTPs**                 | How the attacker operates                              |
| **Threat Intelligence**  | The bigger picture that helps make a security decision |

## SOC Analyst Mindset

Don't stop at:

> **"This IP is malicious."**

Ask:

```text
Why is it malicious?
What is it associated with?
What malware uses it?
Who uses it?
What TTPs are involved?
Did our systems communicate with it?
Are other hosts affected?
```

### Core Formula

```text
IOC + Context + Behavior + Internal Evidence
                    ↓
             Threat Assessment
                    ↓
              SOC Decision
```
