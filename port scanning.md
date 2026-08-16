port-scanning.md
# Port Scanning


## Definition
Port scanning is the process of checking a system's network ports to
discover which ports are open, closed, or filtered.


Think:
Computer = building
Ports = doors
Port scan = checking the doors


## Attacker's Goal
- Discover available services
- Identify potential attack targets
- Find exposed services
- Gather information for further attacks


## Common Ports
- 22 → SSH
- 80 → HTTP
- 443 → HTTPS
- 21 → FTP
- 25 → SMTP
- 53 → DNS


## How It Works


Attacker → Sends probes to ports → Target responds
                                      ↓
                              Open / Closed / Filtered


An attacker may scan:
- One port
- A range of ports
- Many ports
- Multiple hosts


## Kali Practical


### 1. Find your IP
```bash
ip a

Example:

10.0.2.15
2. Scan your own Kali machine
nmap 10.0.2.15

Example result:

PORT   STATE SERVICE
22/tcp open  ssh

Meaning:

22 → port number
tcp → protocol
open → service is listening
ssh → identified service
3. Scan specific ports
nmap -p 22,80,443 10.0.2.15
4. Scan all TCP ports
nmap -p- 10.0.2.15

Only perform scans against systems you own or are authorized to test.

Port States
Open

A service is listening on the port.

Closed

The host is reachable, but no service is listening.

Filtered

A firewall or network filter prevents Nmap from determining
the port's state clearly.

Evidence / Logs

A port scan can create:

Firewall logs
IDS/IPS alerts
Network monitoring events
Flow/connection records

A common pattern is:

One source IP
      ↓
Many destination ports
      ↓
Short period of time
      ↓
Possible port scan
Important SOC Fields
Timestamp → When did it happen?
Source IP → Who sent the probes?
Destination IP → Which system was scanned?
Destination ports → Which ports were targeted?
Protocol → TCP/UDP
Number of connections/probes → How much activity?
Time interval → How quickly did the scan happen?
Our Lab Observation

We ran:

nmap 10.0.2.15

Nmap reported:

22/tcp open ssh

We then checked:

sudo journalctl --since "5 minutes ago"

The normal system journal did not show an obvious
"Nmap scan detected" event.

Important SOC Lesson

Not every attack appears in every log.

Port scanning may not be visible in normal system/application
logs because the probes occur at the network level.

A SOC may need:

Firewall logs
IDS/IPS
Network monitoring
Flow data
SIEM alerts
SOC Investigation

If a port-scan alert appears:

Identify the source IP.
Identify the target system.
Check which ports were scanned.
Check how many ports were contacted.
Check the time period.
Determine whether the source is authorized.
Look for follow-up activity against discovered services.

Example:

10.0.2.50
    ↓
Port 21
Port 22
Port 23
Port 25
Port 53
Port 80
Port 443
    ↓
Many ports in a short time
    ↓
Possible reconnaissance
Detection

A basic detection idea is:

Alert when one source contacts an unusually large number
of destination ports within a short period.

However, legitimate scanners and security tools can create the
same pattern, so the source and context must be checked.

Prevention / Response
Restrict unnecessary exposed services
Use firewalls
Monitor network traffic
Use IDS/IPS
Investigate unexpected scanning
Restrict access to sensitive services
Key Takeaway

Port scanning = discovering exposed network services.

For SOC analysis, focus on:

**Source IP + Destination IP + Ports + Time + Number of probes

Follow-up activity**

Important lesson:

Port scanning is reconnaissance. It may be the first step before
an attacker attempts to exploit a discovered servic
