# Network Traffic Investigation

## What is Network Traffic Investigation?

Network traffic investigation means analyzing network connections to determine:

* Who is communicating?
* Where are they communicating?
* Which service/port is being used?
* Which protocol is being used?
* What domain is involved?
* Does the traffic look normal or suspicious?

---

## 1. Source IP

**Source IP = where the connection comes FROM.**

```text
Source IP: 192.168.1.25
```

Ask:

* Which device owns this IP?
* Which user was using it?
* Is the device internal or external?
* Is the device showing other suspicious activity?

---

## 2. Destination IP

**Destination IP = where the connection is going TO.**

```text
Destination IP: 91.XX.XX.10
```

Investigate:

* Is it internal or external?
* Is it known/trusted?
* Is its reputation suspicious?
* Are multiple machines contacting it?
* Is the connection expected?

---

## 3. Port

**Port = identifies the service/application being accessed.**

| Port | Common Use |
| ---: | ---------- |
|   21 | FTP        |
|   22 | SSH        |
|   23 | Telnet     |
|   25 | SMTP       |
|   53 | DNS        |
|   80 | HTTP       |
|  443 | HTTPS      |
| 3389 | RDP        |
|  445 | SMB        |

> A suspicious port does not automatically mean malicious activity.

> A normal port such as **443** does not automatically mean safe activity.

---

## 4. Protocol

**Protocol = how the communication happens.**

Common protocols:

* **TCP** → reliable connection
* **UDP** → connectionless/faster communication
* **ICMP** → network diagnostic/control traffic
* **DNS** → domain-name resolution
* **HTTP/HTTPS** → web communication

Example:

```text
TCP → Port 443 → HTTPS
```

---

## 5. Domain

**Domain = human-readable name associated with a network destination.**

Example:

```text
unknown-update.com
```

Investigate:

* Domain reputation
* Domain age
* DNS records
* Related IP addresses
* Other endpoints contacting it
* Whether the domain matches the expected application

### Important

```text
Unknown ≠ Malicious
```

An unknown domain is a **reason to investigate**, not proof of compromise.

---

## 6. Traffic Patterns

**Traffic pattern = how network communication behaves over time.**

Look for:

### Repeated connections

```text
10:00 → Server
10:05 → Server
10:10 → Server
10:15 → Server
```

Could indicate **beaconing/C2**, especially when combined with other suspicious evidence.

### Large outbound transfers

```text
Internal PC → External IP
5 GB transferred
```

May require investigation for possible **data exfiltration**.

### Many destinations

```text
One PC → Hundreds of IPs
```

Could indicate scanning, malware activity, or legitimate software behavior.

### Unusual communication

```text
Workstation → Unknown external server
Port: 4444
```

Investigate the process, destination, and context.

---

# SOC Investigation Flow

When you see a suspicious network connection:

```text
Source IP
    ↓
Identify the endpoint/user
    ↓
Destination IP
    ↓
Investigate destination
    ↓
Port + Protocol
    ↓
Domain
    ↓
Traffic Pattern
    ↓
Identify the Process
    ↓
Investigate File/Hash
    ↓
Check Persistence
    ↓
Correlate with Other Logs
```

## Key Questions

```text
WHO?
→ Source IP / User / Host

WHERE?
→ Destination IP / Domain

WHAT?
→ Port / Service

HOW?
→ Protocol

WHEN & HOW OFTEN?
→ Traffic pattern

WHICH PROCESS?
→ Process responsible for connection

IS IT SUSPICIOUS?
→ Correlate all evidence
```

## Example

```text
Source:      192.168.1.25
Destination: 91.XX.XX.10
Port:        443
Protocol:    TCP
Domain:      unknown-update.com

Pattern:
Connection every 5 minutes
```

### Initial Assessment

> An internal endpoint is repeatedly communicating with an external destination over TCP/443 every five minutes. The regular pattern may indicate beaconing/C2 activity. The domain also requires investigation.

### Next Investigation

```text
Which process created the connection?
        ↓
Process path
        ↓
File hash
        ↓
File reputation
        ↓
Process creation time
        ↓
Persistence mechanisms
        ↓
Other suspicious activity
```

## Remember

> **One indicator rarely proves compromise. Correlation makes the case stronger.**

```text
IP + Port + Protocol + Domain + Pattern + Process + Endpoint Evidence
                         ↓
                 Better Investigation
```
