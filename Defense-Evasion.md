# 🛡️ Defense Evasion — SOC L1

## 🔹 What is Defense Evasion?

**Defense Evasion** = techniques attackers use to **avoid detection, hide activity, or remove evidence**.

```text
Attack
  ↓
Try to hide activity
  ↓
Make detection harder
  ↓
Defense Evasion
```

---

## 🔹 Common Techniques

### 1. Disable Security Tools

Attackers may try to disable or weaken:

* Antivirus
* EDR
* Firewall
* Security monitoring

```text
Malware
  ↓
Disable security tool
  ↓
Continue activity
```

---

### 2. Clear Logs

Attackers may remove logs to hide evidence.

```text
Attack
  ↓
Logs record activity
  ↓
Attacker clears logs
  ↓
Less evidence
```

---

### 3. Obfuscation

**Obfuscation** = making commands or code harder to understand or detect.

Example:

```text
powershell.exe -enc XXXXX
```

`-enc` → encoded PowerShell command.

> Encoding alone does NOT prove malicious activity.

---

### 4. Masquerading

**Masquerading** = making something look legitimate.

Example:

```text
C:\Users\User\Downloads\svchost.exe
```

`svchost.exe` is a legitimate Windows process name, but a copy running from an unusual location deserves investigation.

---

### 5. Hide Files

Attackers may place files in locations users normally don't check.

Example:

```text
C:\Users\User\AppData\...
```

Ask:

> Why is this file running from this location?

---

### 6. Process Injection

Malicious code may be placed inside another running process.

```text
Malware
  ↓
Inject code
  ↓
Legitimate process
```

Goal: make malicious activity harder to identify.

---

## 🔹 SOC Investigation

Look for:

```text
Security tool disabled?
Logs cleared?
Encoded/obfuscated commands?
Suspicious file name?
Unusual file location?
Unexpected process behavior?
```

---

## 🔹 Important Rule

Don't judge one event alone.

❌ `PowerShell = malware`

✅

```text
PowerShell
+
Encoded command
+
Downloads script
+
External connection
```

Multiple clues together provide stronger evidence.

---

## 🧠 Mental Model

```text
ATTACK
```
