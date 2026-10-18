— SOC Investigation Ticket
Suspicious Authentication Activity
High priority


Incident type
Suspected brute-force / password guessing

Source IP
10.10.5.44


Affected accounts
bob, administrator, svc_backup

Status
Needs further investigation



Evidence collected
- 15 unique failed logon events — Event ID 4625.
- 3 unique successful logon events — Event ID 4624.
- 2 unique special-privilege events — Event ID 4672.
- Each account had five failures followed by one success in the simulated dataset.
- administrator and svc_backup had special-privilege events.
- Duplicate events were removed using dedup before counting.
  
  Analyst assessment
The sequence is consistent with possible password guessing followed by successful authentication. Privileged access makes the activity especially important to investigate. The available evidence does not yet prove account compromise or identify the real endpoint machines.

Recommended response
1. Escalate to the incident-response team.
2. Verify which device owns 10.10.5.44 and whether the logons were authorized.
3. Review logon types, logon IDs, and activity after each successful login.
4. Follow the organization's incident-response process for containing the source and securing affected accounts if compromise is suspected.
