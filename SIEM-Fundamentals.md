SIEM — Security Information and Event Management
🔹 What is SIEM?

A SIEM collects logs from different systems, analyzes them, detects suspicious activity, and generates alerts for SOC analysts.

Logs → SIEM → Analyze → Detect → Alert → Investigate
🔹 Main SIEM Functions
1. Log Ingestion 📥

Brings logs from different sources into the SIEM.

Windows + Firewall + VPN + Servers → SIEM
2. Parsing 🔍

Breaks a raw log into useful fields.

Raw Log
↓
User | IP | Time | Event
3. Normalization 🧹

Converts different log formats into a common format.

"Failed logon"
"Failed password"
"Authentication failure"
        ↓
Authentication Failure
4. Correlation 🔗

Connects multiple related events to identify a suspicious pattern.

Failed Logins
      ↓
Successful Login
      ↓
PowerShell
      ↓
File Download
      ↓
🚨 Suspicious Activity
5. Detection Rules 🎯

Rules that tell the SIEM what suspicious behavior to detect.

IF 20 failed logins
FROM same IP
WITHIN 5 minutes
→ 🚨 Alert
6. Dashboards 🖥️

Visual screens showing important security information.

Alerts | Failed Logins | Malware | Users | Traffic
7. Alerts 🚨

Warnings generated when suspicious activity matches a detection rule.

Alert = "Something needs investigation."

8. Searches 🔎

Used by analysts to manually search and investigate logs.

Search → IP
Search → Username
Search → Event ID
Search → PowerShell activity
🧠 Easy Summary
📥 Ingest     → Bring logs in
🔍 Parse      → Break logs into fields
🧹 Normalize  → Make logs consistent
🔗 Correlate  → Connect related events
🎯 Detect     → Apply detection rules
🚨 Alert      → Warn the analyst
🖥️ Dashboard  → Show security activity
🔎 Search     → Investigate logs
⭐ One-line definition

SIEM = A central security system that collects, organizes, analyzes, and correlates logs to detect suspicious activity and alert SOC analysts.
