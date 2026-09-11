# Incident Response — Preparation

## 📌 What is Preparation?

Preparation is everything an organization does **before an incident happens** to ensure it can detect, investigate, contain, and recover from attacks effectively.

---

## 🎯 Main Goals

* Prepare people and responsibilities
* Prepare security tools
* Collect useful logs
* Create response procedures
* Prepare communication channels
* Maintain backups
* Practice incident response

---

## 👥 1. People & Roles

Define who is responsible for:

* SOC monitoring
* Incident investigation
* IT / Network response
* Management notification
* Legal / Compliance
* Incident escalation

**SOC L1:** Monitor alerts, investigate initial evidence, correlate logs, document findings, and escalate when necessary.

---

## 📋 2. Incident Response Plan

Defines **what the organization should do when an incident occurs**.

Usually includes:

* Roles & responsibilities
* Severity levels
* Investigation process
* Escalation process
* Containment procedures
* Recovery procedures
* Communication
* Documentation

---

## 📖 3. Playbooks

A playbook gives specific instructions for a particular incident.

Examples:

```text
Brute Force
Phishing
Malware
Ransomware
Account Compromise
DDoS
Data Exfiltration
```

**IR Plan = overall strategy**
**Playbook = procedure for a specific incident**

---

## 📊 4. Logging & Monitoring

Collect logs that can provide evidence during an investigation.

### Important sources:

```text
Authentication
Windows Events
PowerShell
EDR / Antivirus
Firewall
DNS
VPN
Proxy
Cloud
Network
```

Without useful logs, investigation becomes difficult.

---

## 🕐 5. Time Synchronization

Systems should have synchronized clocks, commonly using **NTP**.

Why?

Accurate timestamps allow analysts to build an incident timeline.

Example:

```text
20:01 → Failed logins
20:05 → Successful login
20:05 → PowerShell execution
20:06 → Network connection
```

---

## 🖥️ 6. Asset Inventory

Know what systems exist and how important they are.

Examples:

* Workstations
* Servers
* Databases
* Network devices
* Cloud resources
* Critical applications

A compromised **employee laptop** and a compromised **domain controller** have very different impact.

---

## 🗺️ 7. Network Visibility

Maintain knowledge of the network architecture.

Example:

```text
Internet
   ↓
Firewall
   ↓
DMZ
   ↓
Web Server
   ↓
Internal Network
   ↓
Database
```

This helps determine what systems could be affected.

---

## 🛠️ 8. Security Tools & Access

Authorized responders should have access to necessary tools:

```text
SIEM
EDR
Firewall
VPN
Email Security
Threat Intelligence
Ticketing System
Cloud Security Tools
```

Access should be tested **before** an incident.

---

## 💾 9. Backups

Maintain reliable and protected backups.

Important considerations:

* Offline backups
* Immutable backups
* Access controls
* Regular backup testing

Especially important for **ransomware recovery**.

---

## 📞 10. Communication & Escalation

Define:

```text
Who investigates?
Who gets notified?
Who can contain the incident?
Who contacts management?
When is legal/compliance involved?
```

This prevents confusion during an incident.

---

## 🔐 11. Evidence Preservation

Avoid destroying evidence during response.

Potential evidence:

```text
Logs
Processes
Network connections
Files
Memory
Disk artifacts
Timestamps
Authentication activity
```

**Investigate first; don't blindly delete or modify evidence.**

---

## 🧪 12. Tabletop Exercises

Practice incident response without a real attack.

Example:

> "Ransomware has infected a finance employee's laptop. What do we do?"

The team practices:

```text
Detection
↓
Investigation
↓
Escalation
↓
Containment
↓
Recovery
```

---

# 🧠 Key Takeaway

**Preparation = having the people, processes, tools, logs, access, backups, communication, and playbooks ready BEFORE an incident happens.**

### IR Lifecycle

```text
Preparation
     ↓
Detection & Analysis
     ↓
Containment
     ↓
Eradication
     ↓
Recovery
     ↓
Lessons Learned
```
