# Incident Response — Recovery

## 🔄 What is Recovery?

Recovery is the process of **restoring affected systems to normal, secure operation after eradication**.

> **Goal: Safely return systems and services to normal operation.**

---

## 🎯 Main Goals

* Restore affected systems
* Restore trusted data
* Verify security
* Test system functionality
* Monitor for reinfection
* Safely return systems to production

---

## 1. ✅ Confirm Eradication

Before recovery, verify that:

```text id="p7z9hx"
Malware removed
Persistence removed
Compromised accounts secured
Vulnerability patched
No suspicious activity remains
```

---

## 2. 💻 Restore / Rebuild

Depending on the incident:

```text id="0x7k9m"
Clean backup
    ↓
Restore system
```

or:

```text id="x3b5q1"
Compromised system
    ↓
Reimage / rebuild
    ↓
Install trusted software
```

A heavily compromised system may be **reimaged instead of manually cleaned**.

---

## 3. 🔧 Patch & Harden

Before returning the system to normal:

* Install security patches
* Remove unnecessary software
* Disable unnecessary services
* Apply security configurations
* Update security tools
* Verify endpoint protection

---

## 4. 🧪 Test the System

Check that:

* Applications work correctly
* Network connectivity works
* Security controls are active
* Authentication works
* Required business functions work

---

## 5. 🌐 Reconnect to Network

Only after the system is sufficiently secure:

```text id="8m0j2c"
Clean + Patched + Tested
          ↓
     Reconnect
          ↓
      Monitor
```

---

## 6. 👀 Post-Recovery Monitoring

Continue monitoring for signs of reinfection.

Important sources:

```text id="9yq3wb"
SIEM
EDR
Authentication logs
Firewall logs
DNS logs
Network activity
```

Look for:

* Repeated malware alerts
* Suspicious logins
* Unexpected processes
* Suspicious outbound connections
* New persistence
* Repeated exploitation attempts

---

## 7. 📊 Validate Recovery

Recovery isn't finished just because the machine is online.

Ask:

> **Is the system both secure AND functioning normally?**

If suspicious activity returns:

```text id="k2l7vx"
Recovery
   ↓
Suspicious activity
   ↓
Re-investigate
   ↓
Contain again
```

---

# 🆚 Containment → Eradication → Recovery

| Phase           | Goal                                               |
| --------------- | -------------------------------------------------- |
| **Containment** | Stop the attack from spreading                     |
| **Eradication** | Remove attacker, malware, persistence & root cause |
| **Recovery**    | Safely restore normal operations                   |

### 🧠 Remember

**Containment:**

> Stop it.

**Eradication:**

> Remove it.

**Recovery:**

> Restore it safely.

---

# 🔥 Example

```text id="r4q2nd"
WIN-PC-07 compromised
        ↓
Isolate machine
        ↓
Remove malware & persistence
        ↓
Reset credentials
        ↓
Patch vulnerability
        ↓
Restore/reimage system
        ↓
Test security & functionality
        ↓
Reconnect
        ↓
Monitor for reinfection
```
