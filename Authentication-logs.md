Authentication Logs
What are Authentication Logs?

Authentication logs record attempts to prove an identity when someone tries to access a system.

They help a SOC analyst determine:

WHO → WHEN → WHERE FROM → HOW → SUCCESS/FAILURE → WHAT HAPPENED AFTER
🎯 Why Authentication Logs Matter

Authentication logs can help detect:

Brute-force attacks
Password spraying
Stolen credentials
Unauthorized access
Suspicious RDP logins
Account compromise
Privilege escalation
🪟 Important Windows Event IDs
Event ID	Meaning
4624	Successful logon
4625	Failed logon
4634	Account logged off
4647	User initiated logoff
4648	Logon using explicit credentials
4672	Special privileges assigned
4740	Account locked out
4768	Kerberos TGT requested
4769	Kerberos service ticket requested
4771	Kerberos pre-authentication failed
4776	Domain Controller validated credentials
⭐ Remember first
4624 = SUCCESS
4625 = FAILURE
4672 = PRIVILEGED LOGON
4740 = ACCOUNT LOCKED
🔑 Important Logon Types
Logon Type	Meaning
2	Interactive/local login
3	Network logon
4	Batch
5	Service
7	Computer unlocked
8	NetworkCleartext
9	NewCredentials
10	RemoteInteractive / RDP
11	CachedInteractive
⭐ Remember
2  → Local
3  → Network
5  → Service
10 → RDP
🚨 Common Authentication Attacks
1. Brute Force

Attacker tries many passwords against one account.

Sarah → failed
Sarah → failed
Sarah → failed
Sarah → failed
Sarah → SUCCESS
Pattern
ONE ACCOUNT
     ↓
MANY ATTEMPTS
2. Password Spraying

Attacker tries one or a few passwords against many accounts.

Sarah → failed
Ahmed → failed
Ali → failed
Maria → failed
John → SUCCESS
Pattern
MANY ACCOUNTS
      ↓
FEW PASSWORD ATTEMPTS
3. Credential Stuffing

Attacker uses previously stolen username/password combinations against another service.

Leaked credentials
       ↓
Try same credentials
       ↓
Different service
Key difference
Brute Force      → Guess passwords
Password Spray   → Try few passwords against many users
Credential Stuffing → Reuse stolen credentials
🔎 How to Analyze an Authentication Log

Ask these questions:

1. WHO?
Which account was targeted?
2. WHEN?
What time did it happen?
3. WHERE?
What was the source IP?
4. HOW?
What was the Logon Type?
Was it RDP, network, local, service, etc.?
5. RESULT?
4624 → Success
4625 → Failure
6. WHAT HAPPENED AFTER?

Look for:

New processes
PowerShell
Command Prompt
Privilege changes
File access
Account changes
Network connections
🧠 Example Investigation
4625 Administrator
4625 Administrator
4625 Administrator
4625 Administrator
4624 Administrator
4672 Administrator

Possible story:

Multiple failed logins
        ↓
Successful login
        ↓
Special privileges assigned
Initial assessment

Suspicious authentication activity requiring investigation.

Don't immediately say:

"Confirmed attack."

You need additional evidence.

⚠️ Important SOC Rule

A suspicious authentication event ≠ confirmed compromise.

Always investigate:

Source IP
   ↓
Account
   ↓
Logon Type
   ↓
Failed/Successful attempts
   ↓
User confirmation
   ↓
Post-login activity
📝 Quick Cheat Sheet
4624 → Successful login
4625 → Failed login
4672 → Special privileges
4740 → Account locked

Logon Type 2  → Local login
Logon Type 3  → Network login
Logon Type 5  → Service
Logon Type 10 → RDP

Brute Force
→ One account + many password attempts

Password Spraying
→ Many accounts + few password attempts

Credential Stuffing
→ Stolen credentials reused elsewhere
🎯 SOC Analyst Mindset

Don't ask only:

"Is this malicious?"

Ask:

"What evidence supports or contradicts malicious activity?"

That distinction is what separates reading logs from actually analyzing them.
