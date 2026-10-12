# SOC Incident Ticket — Brute-Force Login Attempt

**Ticket ID:** SOC-2026-001  
**Status:** Investigating  
**Date:** 12 October 2026

### 1. Alert Details

- **Title:** Multiple Failed Login Attempts Against a User Account
- **Severity:** Medium — provisional
- **Detection Source:** SIEM / Windows Security Logs
- **Detection Name:** Possible Brute-Force Login Attempt
- **Timestamp:** 2026-10-12 10:15:00 UTC
- **Affected Host:** WIN-CLIENT01
- **Affected User:** alice
- **Source IP:** 203.0.113.25 (fictional documentation IP)
- **Destination IP:** 192.168.1.20 (fictional lab IP)

### 2. Evidence Collected

- 15 failed login attempts were recorded within two minutes.
- Windows Event ID 4625 recorded failed logon attempts.
- The attempts targeted the same user account.
- No successful login was identified in the time window reviewed.
- Source IP reputation and related host activity have not yet been verified.

### 3. Investigation

The analyst reviewed authentication logs for the affected user and host. A high number of failed login attempts occurred within a short period, which is consistent with possible password guessing.

The analyst checked the reviewed time window for a subsequent successful login. No successful login was identified in that window. Additional checks are required to determine whether the source IP is legitimate, whether other accounts were targeted, and whether suspicious activity occurred before or after the alert.

### 4. MITRE ATT&CK Mapping

- **Tactic:** Credential Access
- **Technique:** T1110 — Brute Force
- **Mapping Confidence:** Possible match; requires further investigation.

### 5. Analyst Verdict

**Verdict:** Suspicious activity requiring further investigation.

The failed login pattern is consistent with a possible brute-force attempt. However, the available evidence does not confirm that an account was compromised or establish who initiated the attempts.

### 6. Recommended Actions

1. Review authentication logs before and after the alert.
2. Check for successful logins, other targeted accounts and unusual source IP activity.
3. Verify whether the source IP belongs to an authorized user, VPN or internal system.
4. Escalate according to SOC policy if the activity continues or additional evidence indicates compromise.
5. Apply account protection or blocking measures only in accordance with authorization and incident response procedures.
6. Document all findings and update the ticket after further investigation.

### 7. Closure Criteria

Keep the ticket open until the activity is sufficiently investigated and the analyst can support a final classification. Record the final verdict, evidence, actions taken and escalation outcome before closure.
