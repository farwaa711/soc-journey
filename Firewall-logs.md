Firewall Logs — Short SOC Notes
1. What is a Firewall?

A firewall controls network traffic between systems.

Source → Firewall → Destination

It decides:

ALLOW = let traffic through
DENY  = block traffic
2. What does a Firewall Log tell you?

A firewall log answers:

Who communicated with whom, using what service, and was it allowed?

Important fields:

Field	Meaning
Source IP	Who started the connection
Destination IP	Who was contacted
Source Port	Client-side port
Destination Port	Service being accessed
Protocol	TCP / UDP / ICMP
Action	ALLOW / DENY
Timestamp	When it happened
3. IP vs Port
10.10.10.15:3389
     ↑       ↑
    IP      Port

IP = which computer

Port = which service

Common ports:

22    → SSH
53    → DNS
80    → HTTP
443   → HTTPS
445   → SMB
3389  → RDP
4. Internal vs External

Private IP examples:

10.x.x.x
172.16.x.x – 172.31.x.x
192.168.x.x

Usually:

External IP → Company IP

means an Internet system is communicating with a company system.

Internal IP → Internal IP

means communication is happening inside the company network.

Internal does NOT automatically mean safe.

5. Why does SOC care?

Because the SOC asks:

Is this communication expected or suspicious?

Example:

45.88.10.20 → 10.10.10.15:3389 → DENY

Means:

External IP tried to reach the company's RDP service, but the firewall blocked it.

One event doesn't prove an attack.

6. Patterns to Look For
One IP → Many ports
Attacker → :22
         → :80
         → :443
         → :3389

Possible port scanning.

One IP → Many internal computers
External IP
   ↓
PC1
PC2
PC3
PC4

Possible scanning.

Internal computer → unusual external IP
Company PC → Unknown Internet IP

Possible malware/C2, depending on context.

Repeated connections
20:00 → connection
20:01 → connection
20:02 → connection
20:03 → connection

Possible beaconing, but needs investigation.

7. Firewall + Authentication

These logs can connect.

Firewall:
External IP → Server:3389 → ALLOW
              ↓
Authentication:
4624 → Login SUCCESS

Now investigate:

Who logged in and was that login legitimate?

Remember:

Firewall ALLOW ≠ successful login.

8. Firewall + Endpoint

After successful access:

Firewall
   ↓
RDP ALLOW
   ↓
Authentication
   ↓
Login SUCCESS
   ↓
Endpoint logs
   ↓
What did the user/process do?

Look for:

PowerShell
suspicious processes
file creation
credential access
lateral movement
persistence
9. Simple Investigation Flow
1. WHO?
   Source IP

2. WHO?
   Destination IP

3. WHAT?
   Destination port/service

4. ACTION?
   ALLOW or DENY

5. NORMAL?
   Is this expected?

6. PATTERN?
   Repeated? Many ports? Many hosts?

7. CORRELATE
   Authentication + Endpoint + DNS logs

8. CONCLUDE
   Normal / Suspicious / Malicious
10. Golden Rule

Don't investigate an IP just because it exists. Investigate the communication and its context.

IP → Port → Connection → Authentication → Activity

That is the core firewall-log workflow.
