DNS Logs

DNS = Domain Name System
Converts domain names into IP addresses.

🔹 Basic Flow
User visits google.com
        ↓
Computer asks DNS
        ↓
DNS finds IP address
        ↓
Computer connects to that IP
🔹 Important DNS Log Fields
Field	Meaning
Time	When the DNS request happened
Source IP	Internal device that made the request
Query	Domain being requested
Response IP	IP returned by DNS
Status	Result of the DNS request
🔹 Example
Time       Source IP     Query          Response
10:15:01   10.0.0.25     google.com     142.250.x.x

Meaning:

10.0.0.25 asked:
"What is the IP of google.com?"
🚨 Suspicious DNS Signs

Look for:

Random-looking domain names
Very long domain names
Many DNS requests in a short time
Same machine repeatedly contacting one strange domain
Many NXDOMAIN responses
Strange/random subdomains
Regular repeated requests → possible beaconing
One machine contacting many unusual domains
🔹 NXDOMAIN
NXDOMAIN = Domain does not exist

One NXDOMAIN → usually not enough.

Many random NXDOMAIN requests from one machine → investigate.

🔹 DNS Beaconing

Repeated requests at regular intervals:

10:00 → suspicious.com
10:05 → suspicious.com
10:10 → suspicious.com
10:15 → suspicious.com

Could indicate malware communicating with a server.

🔹 DNS Tunneling

Attackers may abuse DNS to hide data inside DNS requests.

Example:

x82jd92.example.com
k29sd82.example.com
91kd82.example.com

Lots of strange subdomains from one machine → investigate.

🔍 L1 Investigation

When you find a suspicious DNS request:

1. Identify the source IP
2. Identify the user/device
3. Check the domain
4. Check how often it was requested
5. Check the response IP
6. Check firewall/network logs
7. Check the process that made the request
8. Check PowerShell/process logs
9. Check downloaded files
10. Check what happened afterward
⭐ Key SOC Rule
DNS alone = CLUE
DNS + Process + Network + File activity = STRONGER EVIDENCE

Don't immediately say:

"This domain is malicious."

Say:

"This DNS activity is suspicious and requires further investigation."
