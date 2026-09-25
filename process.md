# 🖥️ Processes — SOC Analyst Notes

## 1. What is a Process?

> **A process is a program that is currently running.**

```text
Program
  ↓
Started/Running
  ↓
Process
```

### Example

```text
chrome.exe installed
      ↓
User opens Chrome
      ↓
chrome.exe is running
      ↓
Process
```

### Remember

```text
Program  = software
Process  = running software
```

---

# 2. Why Processes Matter in SOC

Attackers need to **run programs/commands** to perform actions.

Example:

```text
Malicious File
      ↓
PowerShell
      ↓
Command
      ↓
Download file
```

A SOC analyst investigates the **process chain** to understand what happened.

---

# 3. Process ID (PID)

**PID = Process ID**

Every running process gets a number.

Example:

```text
powershell.exe
PID: 5704
```

PID helps identify a specific running process.

Think:

```text
Process → PID → Identification
```

---

# 4. Parent Process

The process that **starts another process**.

Example:

```text
Word
 ↓
PowerShell
```

Here:

```text
Word = Parent
PowerShell = Child
```

---

# 5. Child Process

The process that was **started by another process**.

```text
Word
 ↓
PowerShell
```

```text
PowerShell = Child
```

---

# 6. Process Tree

A process tree shows **parent → child relationships**.

Example:

```text
WINWORD.EXE
     ↓
powershell.exe
     ↓
cmd.exe
     ↓
malware.exe
```

Read it as:

```text
Word started PowerShell
PowerShell started cmd
cmd started malware.exe
```

### SOC Question

> **Who started whom?**

This is one of the most useful questions when investigating processes.

---

# 7. Process Creation

When a program starts another program:

```text
Program A
   ↓
starts
   ↓
Program B
```

A new process has been **created**.

---

# 8. Spawn

**Spawn = create/start another process.**

Example:

```text
PowerShell
   ↓
spawns
   ↓
cmd.exe
```

Meaning:

> PowerShell started `cmd.exe`.

---

# 9. Process Termination

When a running process stops:

```text
Process starts
     ↓
Process runs
     ↓
Process stops
```

The process has **terminated**.

```text
Creation → Process starts
Termination → Process stops
```

---

# 10. Command Line

The command line shows **what instructions/options were given to a process**.

Example:

```text
powershell.exe -ExecutionPolicy Bypass -File sample.ps1
```

### Breakdown

```text
powershell.exe
        ↓
Process

-ExecutionPolicy Bypass
        ↓
Option

-File sample.ps1
        ↓
Script to run
```

### SOC Tip

Don't only look at the process name.

Look at the **full command line**.

---

# 11. Suspended Process

A suspended process is created but **paused**.

Normal:

```text
Create
  ↓
▶️ Run
```

Suspended:

```text
Create
  ↓
⏸️ Paused
```

Attackers can sometimes use suspended processes during **Process Injection**.

---

# 12. Process Injection

Process Injection means:

> **Putting code into another running process.**

Simple idea:

```text
Attacker Code
      ↓
Another Process
      ↓
Code runs inside it
```

MITRE ATT&CK:

```text
T1055 = Process Injection
```

---

# 13. Important Process Examples

### Normal

```text
Windows Terminal
      ↓
PowerShell
```

Could be normal.

### Suspicious Example

```text
Word document
      ↓
PowerShell
      ↓
cmd.exe
      ↓
Unknown executable
```

This deserves investigation because the process chain may be unusual.

> **Important:** An unusual process chain is a signal to investigate, not automatic proof of malware.

---

# 14. SOC Process Investigation

When you see a suspicious process, ask:

### 1. What process ran?

```text
powershell.exe
```

### 2. Who started it?

```text
WINWORD.EXE
    ↓
powershell.exe
```

### 3. What command did it run?

```text
powershell.exe -ExecutionPolicy Bypass -File sample.ps1
```

### 4. What did it start next?

```text
PowerShell
   ↓
cmd.exe
```

### 5. What did it do afterward?

Check:

```text
📁 Files
🌐 Network
📝 Registry
🔐 Security settings
```

### 6. Is it normal in this situation?

Consider:

```text
User
Time
Parent process
Command line
File location
Network activity
Expected business activity
```

---

# 15. Process + Behavior

Processes are one part of **Behavior Analysis**.

```text
Process
   ↓
What was executed?
   ↓
What did it create?
   ↓
What did it contact?
   ↓
What did it change?
```

Example:

```text
Word
 ↓
PowerShell
 ↓
Creates script
 ↓
Contacts IP
 ↓
Downloads file
```

---

# 16. Process + MITRE ATT&CK

Observed process behavior can be mapped to MITRE ATT&CK.

Example:

```text
PowerShell executed
       ↓
Command & Scripting Interpreter
       ↓
PowerShell
       ↓
T1059.001
```

Another example:

```text
Code placed into another process
       ↓
Process Injection
       ↓
T1055
```

### Remember

```text
Behavior = What happened?

MITRE = Standard label for that behavior
```

---

# 🧠 Quick Reference

| Term                  | Simple Meaning                          |
| --------------------- | --------------------------------------- |
| **Program**           | Software                                |
| **Process**           | Running program                         |
| **PID**               | Process identification number           |
| **Parent**            | Process that starts another process     |
| **Child**             | Process started by another process      |
| **Process Tree**      | Parent → child relationships            |
| **Spawn**             | Start/create another process            |
| **Command Line**      | Instructions/options given to a process |
| **Creation**          | Process starts                          |
| **Termination**       | Process stops                           |
| **Suspended**         | Process is paused                       |
| **Process Injection** | Code placed into another process        |

---

# 🔥 SOC Mental Model

```text
Suspicious Process
       ↓
WHAT?
       ↓
Which process?
       ↓
WHO?
       ↓
Who started it?
       ↓
HOW?
       ↓
What command line?
       ↓
WHAT NEXT?
       ↓
What processes/files/network activity followed?
       ↓
NORMAL OR SUSPICIOUS?
       ↓
Investigate
```

## ⭐ Golden Rule

> **Don't investigate a process only by its name.**

Always look at:

```text
Process
+
Parent
+
Command Line
+
Child Processes
+
Files
+
Network
+
Context
```

That's the foundation of **process analysis for a SOC analyst**.
