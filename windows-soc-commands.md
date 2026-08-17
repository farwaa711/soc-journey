# Windows SOC Useful Commands

A practical collection of Windows commands useful for **L1 SOC investigations**.

> **Goal:** Understand what evidence each command provides. You do not need to memorize every command or option. It is completely fine to look up exact syntax when needed.

---

## 1. Current User & Identity

### `whoami`

Shows the username of the account currently running the command.

```cmd
whoami
```

**SOC use:** Identify which account is currently being used.

---

### `whoami /user`

Shows the current username and its **SID (Security Identifier)**.

```cmd
whoami /user
```

**SOC use:** Uniquely identify the account involved in an investigation.

---

### `whoami /groups`

Shows the groups the current account belongs to.

```cmd
whoami /groups
```

**SOC use:** Check whether an account belongs to privileged groups such as `Administrators`.

---

### `whoami /priv`

Shows privileges assigned to the current security token.

```cmd
whoami /priv
```

**SOC use:** Investigate potentially important privileges such as `SeShutdownPrivilege`.

---

## 2. User Accounts

### `net user`

Lists local user accounts.

```cmd
net user
```

**SOC use:** Check what local accounts exist on a Windows machine.

---

### `net user <username>`

Shows information about a specific local user.

```cmd
net user Ahmed
```

**SOC use:** Investigate an account involved in suspicious activity.

---

## 3. Logged-In Users & Sessions

### `query user`

Shows currently logged-in user sessions.

```cmd
query user
```

Useful information can include:

* Username
* Session name
* Session ID
* Session state
* Idle time
* Logon time

**SOC use:** Determine who is currently logged in and when their current session started.

> `query user` is not a complete historical log of every login. For historical investigation, use Windows Security Event Logs.

---

## 4. Windows Security Event Logs

### `Get-WinEvent`

PowerShell command for retrieving Windows event logs.

```powershell
Get-WinEvent -LogName Security -MaxEvents 20
```

Shows the 20 newest events from the Security log.

**SOC use:** Investigate security-related activity recorded by Windows.

---

### Filter by Event ID

Example: successful logons.

```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4624}
```

**Important Security Event IDs:**

| Event ID | Meaning                                        |
| -------- | ---------------------------------------------- |
| 4624     | Successful logon                               |
| 4625     | Failed logon                                   |
| 4634     | Logoff                                         |
| 4648     | Logon using explicit credentials               |
| 4672     | Special privileges assigned to new logon       |
| 4720     | User account created                           |
| 4726     | User account deleted                           |
| 4732     | Member added to a local security-enabled group |
| 4740     | User account locked out                        |

**SOC use:** Build a timeline of authentication and account-related activity.

---

## 5. PowerShell Command History

### `Get-History`

Shows commands entered during the current PowerShell session.

```powershell
Get-History
```

**SOC use:** Useful when examining activity performed during the current PowerShell session.

> This is different from `Get-WinEvent`. Security Event Logs contain Windows security events, not simply a list of every command you typed.

---

## 6. Network Information

### `ipconfig /all`

Shows detailed network configuration.

```cmd
ipconfig /all
```

Can show:

* IP address
* MAC address
* Default gateway
* DNS servers
* DHCP information

**SOC use:** Understand the host's network configuration during an investigation.

---

### `nslookup`

Performs DNS lookups.

```cmd
nslookup example.com
```

**SOC use:** Investigate domain-to-IP resolution.

---

### `netstat -ano`

Shows network connections and listening ports.

```cmd
netstat -ano
```

Important information:

* Local address
* Remote address
* Port
* Connection state
* PID

**SOC use:** Investigate suspicious network connections and identify the process associated with a connection.

---

## 7. Processes

### `tasklist`

Shows running processes.

```cmd
tasklist
```

**SOC use:** Check what processes are currently running on a host.

---

### Find a specific process

```cmd
tasklist | findstr chrome
```

**SOC use:** Quickly search the process list.

---

### `tasklist /svc`

Shows processes and associated Windows services.

```cmd
tasklist /svc
```

**SOC use:** Help connect a suspicious process to a Windows service.

---

## 8. System Information

### `hostname`

Shows the computer's hostname.

```cmd
hostname
```

**SOC use:** Identify the machine being investigated.

---

### `systeminfo`

Displays detailed operating-system and system information.

```cmd
systeminfo
```

Can provide information such as:

* OS version
* Hostname
* System manufacturer
* Installed updates
* System architecture
* Boot time

**SOC use:** Establish basic host context.

---

## 9. Services

### `sc query`

Displays information about Windows services.

```cmd
sc query
```

**SOC use:** Investigate running/stopped services and suspicious service activity.

---

### Check a specific service

```cmd
sc query <service-name>
```

Example:

```cmd
sc query ssh
```

---

## 10. Files & Directories

### `dir`

Lists files and directories.

```cmd
dir
```

**SOC use:** Examine files in a directory during an investigation.

---

### `cd`

Changes the current directory.

```cmd
cd C:\Users\Ahmed\Downloads
```

**SOC use:** Navigate to locations containing potentially suspicious files.

---

### `where`

Finds the location of an executable.

```cmd
where powershell
```

**SOC use:** Determine which executable path Windows is using.

---

# L1 SOC Investigation Flow

When investigating a suspicious Windows machine, think in terms of questions rather than memorizing commands.

### 1. Who is involved?

```cmd
whoami
whoami /user
whoami /groups
whoami /priv
```

### 2. Who is currently logged in?

```cmd
query user
```

### 3. What accounts exist?

```cmd
net user
```

### 4. What happened?

```powershell
Get-WinEvent -LogName Security -MaxEvents 20
```

### 5. Was there a successful or failed login?

```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4624}
```

or:

```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625}
```

### 6. What is the machine's network configuration?

```cmd
ipconfig /all
```

### 7. What network connections exist?

```cmd
netstat -ano
```

### 8. What processes are running?

```cmd
tasklist
```

### 9. What is the machine?

```cmd
hostname
systeminfo
```

---

# What to Memorize vs. What to Look Up

You **do not need to memorize every option**.

### Memorize these concepts:

| Question                            | Command                   |
| ----------------------------------- | ------------------------- |
| Who am I?                           | `whoami`                  |
| What's my SID?                      | `whoami /user`            |
| What groups am I in?                | `whoami /groups`          |
| What privileges do I have?          | `whoami /priv`            |
| What users exist?                   | `net user`                |
| Who is logged in?                   | `query user`              |
| What security events occurred?      | `Get-WinEvent`            |
| What's my IP/network configuration? | `ipconfig /all`           |
| What network connections exist?     | `netstat -ano`            |
| What processes are running?         | `tasklist`                |
| What machine am I investigating?    | `hostname` / `systeminfo` |

### Look up when needed:

* Complex `Get-WinEvent` filters
* Rare `net` options
* Advanced PowerShell syntax
* Unfamiliar Event IDs
* Commands you don't use regularly

> **SOC skill is not memorizing commands.**
>
> The important skill is knowing **what evidence you need, where to find it, and which command/tool can retrieve it.**

---

# Quick Reference

```text
IDENTITY
whoami
whoami /user
whoami /groups
whoami /priv

USERS
net user
net user <username>

SESSIONS
query user

EVENTS
Get-WinEvent -LogName Security -MaxEvents 20

NETWORK
ipconfig /all
nslookup <domain>
netstat -ano

PROCESSES
tasklist
tasklist /svc

SYSTEM
hostname
systeminfo

SERVICES
sc query

FILES
dir
cd
where <program>
```

## Learning Priority

**High priority:**
`whoami`, `whoami /user`, `whoami /groups`, `net user`, `query user`, `Get-WinEvent`, `ipconfig`, `netstat`, `tasklist`

**Medium priority:**
`whoami /priv`, `nslookup`, `systeminfo`, `sc query`, `hostname`

**Look up when needed:**
Rare/advanced command options and complicated PowerShell filters.

This file is intended as a **SOC investigation cheat sheet**, not a list of commands to blindly memorize.
