Windows Event Logs

Windows Event Logs record important activity happening on a Windows computer.

🔹 Main Types
Security
System
Application
PowerShell
Security

Records security-related activity:

Logons
Process creation
Account changes
Privilege use
System

Records Windows/system activity:

Services
Drivers
System errors
Startup/shutdown
Application

Records activity and errors from applications.

PowerShell

Records PowerShell activity and commands.

🔹 Important Event IDs
Event ID	Meaning
4624	Successful login
4625	Failed login
4672	Special privileges assigned to a new logon
4688	New process created
4720	User account created
4728	User added to a security-enabled global group
4732	User added to a security-enabled local group
4740	Account locked out
4104	PowerShell script content
1102	Security audit log was cleared
🔹 4624 — Successful Login

Example:

Event ID: 4624
User: Ahmed
Logon Type: 3
Source IP: 10.0.0.25

Means:

Ahmed successfully logged in.

Important

Check:

User
Source IP
Logon Type
Time
🔹 4625 — Failed Login
Event ID: 4625
User: Ahmed
Source IP: 10.0.0.50

Means:

Someone tried to log in as Ahmed, but authentication failed.

🚨 Suspicious pattern
4625
4625
4625
4625
4625
   ↓
4624

Many failed logins followed by a successful login → investigate.

Possible causes include:

Brute force
Password spraying
Wrong password
Legitimate user mistakes
🔹 4688 — Process Creation
Event ID: 4688
User: Ahmed
Parent: explorer.exe
Process: powershell.exe

Means:

PowerShell was started.

Check:

Parent process
Process name
Command line
User
Time
🔹 4104 — PowerShell
Event ID: 4104
PowerShell:
DownloadString(...)

Means:

PowerShell script content was logged.

🚨 Investigate commands involving:

Download
IEX
EncodedCommand
ExecutionPolicy Bypass
Hidden
WebClient

These are suspicious clues, not automatic proof of malware.

🔹 4672 — Special Privileges
Event ID: 4672
User: Ahmed

Means:

The account received powerful/special privileges during logon.

Investigate when it happens unexpectedly, especially for unusual accounts or times.

🔹 4720 — New Account
Event ID: 4720
User Created: attacker

Means:

A new Windows account was created.

🚨 Investigate if the account wasn't expected.

🔹 4740 — Account Locked
Event ID: 4740
Account: Ahmed

Means:

Ahmed's account was locked because of too many failed authentication attempts.

Look for the failed-login events before it.

🔹 1102 — Security Log Cleared
Event ID: 1102
User: Ahmed

Means:

The Windows Security Event Log was cleared.

🚨 This can be suspicious because an attacker may try to remove evidence.

But legitimate administrators can also clear logs.

🔗 Correlating Windows Logs

Don't investigate one event alone.

Example:

4624
Successful login
     ↓
4688
PowerShell starts
     ↓
4104
PowerShell downloads script
     ↓
Network connection
     ↓
File created
     ↓
Another process starts

This gives you a much stronger picture of what happened.

🔍 L1 Investigation Flow
1. What happened?
        ↓
2. Which Event ID?
        ↓
3. Which user?
        ↓
4. Which computer?
        ↓
5. When?
        ↓
6. Source IP?
        ↓
7. What process/command?
        ↓
8. What happened before?
        ↓
9. What happened afterward?
⭐ Golden Rule

One Windows event is usually a clue. Multiple related events create the story.

Authentication
      ↓
Process
      ↓
PowerShell
      ↓
Network
      ↓
File
      ↓
Follow-up activity

That correlation is the core skill for L1 Windows log analysis.
