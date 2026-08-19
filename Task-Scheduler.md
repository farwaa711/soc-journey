
Windows Task Scheduler — L1 Quick Notes
What is it?

Task Scheduler = Windows automation system.

It automatically runs a program/script when something happens.

Trigger → Action
WHEN?     WHAT?
4 Things to Check
1. Task
Get-ScheduledTask

→ Find tasks.

2. Trigger
$task.Triggers

→ WHEN does it run?

Examples:

Boot → Windows starts
Logon → User logs in
Daily → Runs daily
3. Action
$task.Actions

→ WHAT does it run?

Look at:

Execute → program
Arguments → command/options
4. Principal
$task.Principal

→ WHO runs it?

Look at:

UserId
RunLevel
Investigation Flow
Task
 ↓
WHEN? → Trigger
 ↓
WHAT? → Action
 ↓
WHO?  → Principal
 ↓
WHERE? → File location
 ↓
Does it make sense?
Suspicious Signs

Be more suspicious when you see:

Unknown task
Strange/random task name
.exe or script in Temp, AppData, etc.
Obfuscated PowerShell
Unexpected startup/logon persistence
Unknown program running as SYSTEM

One sign ≠ malware. Look at the whole picture.

Useful Commands
# Find tasks
Get-ScheduledTask


# Select a task
$task = Get-ScheduledTask -TaskName "TaskName"


# WHEN?
$task.Triggers


# WHAT?
$task.Actions


# WHO?
$task.Principal


# Last/next execution
Get-ScheduledTaskInfo -TaskName "TaskName"


# Remove a task you have verified
Unregister-ScheduledTask -TaskName "TaskName"
Memory trick

T → T → A → P

Task → Trigger → Action → Principal

Or simply:

WHEN → WHAT → WHO → IS IT NORMAL?
