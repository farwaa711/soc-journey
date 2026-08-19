Credential Theft — L1 SOC Notes
1. What is Credential Theft?

Credential theft = an attacker attempts to obtain authentication material that can be used to access an account or system.

Examples:

Passwords
Password hashes
Kerberos tickets
Authentication tokens
Stored credentials
LSASS memory
L1 Goal

Answer:

WHO → WHAT → HOW → WHAT HAPPENED NEXT?
2. Important Windows Events
Event ID	Meaning
4624	Successful logon
4625	Failed logon
4648	Logon using explicit credentials
4672	Special privileges assigned
4688	New process created
4768	Kerberos TGT requested
4769	Kerberos service ticket requested
4776	NTLM credential validation
Remember
4688 = PROCESS
4624 = SUCCESSFUL LOGON
4625 = FAILED LOGON
4648 = EXPLICIT CREDENTIALS
3. Event 4688 — Process Creation

When investigating:

Event ID: 4688

Look at:

SubjectUserName
NewProcessName
ParentProcessName
CommandLine

Ask:

WHO created it?
WHAT process?
WHO was the parent?
WHAT command was executed?
4. How to Read CommandLine

Always read left → right.

Example
procdump.exe -ma lsass.exe C:\Temp\memory.dmp

Break it down:

Program → Option → Target → Output
procdump.exe
     ↓
-ma
     ↓
lsass.exe
     ↓
memory.dmp

Interpretation:

ProcDump is instructed to create a full memory dump of lsass.exe and save it as memory.dmp.

Important

Don't interpret a command based only on filenames.

For example:

scan.ps1

doesn't prove that the script performs scanning.

Read the actual command and investigate the file.

5. LSASS

lsass.exe is a legitimate Windows process involved in authentication and security policy enforcement.

Because sensitive authentication material can exist in LSASS memory, attempts to access or dump its memory deserve investigation.

Important distinction
Get-Process lsass

→ Retrieves information about the LSASS process.

Not automatically credential theft.

Whereas:

Tool → LSASS → Memory dump → .dmp file

→ Strong credential-access indicator.

6. Credential-Theft Investigation Pattern
Suspicious process
       ↓
Credential-related target
       ↓
Credential access/dumping behavior
       ↓
Authentication activity
       ↓
Possible account compromise

Don't declare credential theft from one weak indicator.

7. Examples
Normal / Low Concern
powershell.exe -Command "Get-Process lsass"

Meaning:

PowerShell retrieves information about the LSASS process.

Assessment: Not credential theft by itself.

Credential-related
powershell.exe -Command "Get-Credential"

Meaning:

PowerShell prompts for credentials.

Assessment: Credential-related, but legitimate uses exist.

High Concern
procdump.exe -ma lsass.exe C:\Temp\memory.dmp

Meaning:

A full memory dump of LSASS is being written to a file.

Assessment: Strong credential-access indicator.

8. Suspicious Indicators

Investigate combinations such as:

Unusual process accessing LSASS
LSASS memory dump
Unknown executable
Suspicious script execution
Credential-related activity from an unusual user
Process launched from Temp or unusual AppData location
Suspicious parent-child process relationship
Credential activity followed by unusual successful logons
Important
One indicator ≠ malicious

Multiple correlated indicators are much stronger.

9. Useful PowerShell Commands
Find a process
Get-Process lsass
Get process details
Get-Process lsass | Format-List *
Find process creation events
Get-WinEvent -FilterHashtable @{LogName="Security"; Id=4688}
Successful logons
Get-WinEvent -FilterHashtable @{LogName="Security"; Id=4624}
Failed logons
Get-WinEvent -FilterHashtable @{LogName="Security"; Id=4625}
Explicit credential use
Get-WinEvent -FilterHashtable @{LogName="Security"; Id=4648}
10. L1 Investigation Checklist

When you see possible credential theft:

[ ] Identify the user
[ ] Identify the process
[ ] Identify the parent process
[ ] Read the CommandLine
[ ] Identify the credential target
[ ] Check the executable/script location
[ ] Check surrounding authentication events
[ ] Check whether the behavior makes sense
[ ] Correlate multiple events
[ ] Decide: Benign / Suspicious / Malicious / Unknown
11. Mental Model

Remember:

4688
 ↓
WHAT PROCESS?
 ↓
WHAT COMMAND?
 ↓
WHAT TARGET?
 ↓
CREDENTIAL ACCESS?
 ↓
AUTHENTICATION EVENTS?
 ↓
CORRELATE
 ↓
VERDICT
One sentence to remember

Credential-theft detection is not about seeing one suspicious command; it's about identifying abnormal access to authentication material and correlating it with the process, user, command line, and subsequent authentication activity.
