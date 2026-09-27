# 📂 File Execution — SOC L1

## 🔹 What is File Execution?

**File execution** means a file/program is actually **started and runs** on a system.

> A file existing on a computer ≠ the file being executed.

Example:

```text
malware.exe exists
        ↓
User opens it
        ↓
malware.exe executes
```

---

## 🔹 Process Execution

When a program runs, it creates a **process**.

Example:

```text
WINWORD.EXE
     ↓
powershell.exe
```

* `WINWORD.EXE` → **Parent process**
* `powershell.exe` → **Child process**

The process chain helps an analyst understand **what started what**.

---

## 🔹 What to Check

When investigating execution, ask:

```text
1. What executed?
2. Who/what started it?
3. Where is the file located?
4. What command was used?
5. Which user executed it?
6. What happened after execution?
```

---

## 🔹 Important Evidence

### Process

```text
Process: powershell.exe
Parent: WINWORD.EXE
```

### Command Line

```text
powershell.exe -enc XXXXX
```

`-enc` → encoded PowerShell command.

### File Path

```text
C:\Users\User\Downloads\invoice.exe
```

### Hash

```text
SHA-256: abc123...
```

Can be checked against threat-intelligence sources.

### Network Activity

```text
powershell.exe
      ↓
185.XX.XX.24
```

Shows that the process communicated with an external server.

---

## 🔹 Suspicious Execution Example

```text
WINWORD.EXE
     ↓
powershell.exe
     ↓
-enc XXXXX
     ↓
Invoke-WebRequest
     ↓
update.ps1
     ↓
185.XX.XX.24
```

### Why suspicious?

Multiple behaviors appear together:

* Office application starts PowerShell
* PowerShell uses an encoded command
* PowerShell downloads a script
* Connection goes to an external IP

> One event alone does not prove malware. **Context and the full process chain matter.**

---

## 🔹 SOC Mental Model

```text
FILE
 ↓
EXECUTION
 ↓
PROCESS
 ↓
CHILD PROCESS
 ↓
FILE / NETWORK ACTIVITY
 ↓
PERSISTENCE / C2
```

## 🧠 Key Rule

> **Don't just ask "What file exists?" Ask "What executed, who started it, and what happened afterward?"**
