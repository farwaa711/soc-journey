# Incident Response — Containment & Eradication

## 🛑 Containment

### 📌 What is Containment?

Containment is the process of **limiting an active incident** so the attacker cannot continue causing damage or spreading to other systems.

> **Goal: Stop the attack from getting worse.**

### Common Actions

* Isolate compromised endpoint
* Block malicious IP/domain
* Disable compromised account
* Revoke suspicious sessions/tokens
* Restrict network access
* Block malicious files/hashes
* Segment affected systems

### Example

```text
Compromised PC
      ↓
Network isolation
      ↓
Attacker cannot communicate with it
      ↓
Investigation continues
```

**Containment ≠ removing the attacker.**

It temporarily limits the threat.

---

# 🧹 Eradication

### 📌 What is Eradication?

Eradication is the process of **removing the attacker, malware, persistence, and root cause** from the environment.

> **Goal: Make sure the attacker cannot come back through the same compromise.**

### Common Actions

* Remove/quarantine malware
* Remove malicious scripts/files
* Remove persistence mechanisms
* Remove attacker-created accounts
* Reset compromised passwords
* Revoke compromised sessions/tokens
* Rotate compromised keys/secrets
* Patch exploited vulnerabilities
* Rebuild/reimage systems when necessary
* Check for additional compromised systems

### 🔄 Persistence

Persistence allows an attacker to regain access.

Examples:

```text
Scheduled Tasks
Services
Startup Programs
Registry Run Keys
Cron Jobs
SSH authorized_keys
Web shells
Malicious accounts
Cloud access keys
```

**Important:**

```text
Delete malware
      ↓
Persistence remains
      ↓
Malware may return
```

Therefore, remove **both the malicious payload and its persistence mechanism**.

---

# 🔍 Root Cause

Ask:

> **How did the attacker get in?**

Example:

```text
Unpatched application
        ↓
Vulnerability exploited
        ↓
Malware installed
        ↓
Persistence created
```

Eradication should address the **entire chain**, not just the malware.

---

# ✅ Verification

Before declaring eradication complete, verify:

```text
Malicious files       ❌
Malicious processes   ❌
Persistence           ❌
Suspicious accounts   ❌
Unauthorized access   ❌
Compromised credentials ❌
Suspicious connections ❌
Repeated alerts       ❌
Original vulnerability ❌
Reinfection           ❌
```

---

# 🆚 Containment vs Eradication

| Containment        | Eradication         |
| ------------------ | ------------------- |
| Stop the attack    | Remove the attacker |
| Isolate endpoint   | Remove malware      |
| Block malicious IP | Remove persistence  |
| Disable account    | Reset credentials   |
| Restrict network   | Patch vulnerability |
| Temporary control  | Permanent removal   |

### 🧠 Remember

**Containment:**

> *"Stop it from getting worse."*

**Eradication:**

> *"Remove it and prevent it from coming back."*
