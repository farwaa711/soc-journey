# Brute-Force Attack

## Definition
An attack where an attacker repeatedly tries different credentials
to gain unauthorized access.

## Goal
- Gain access to an account/system
- Take over an account
- Access sensitive data

## Types
- Brute force → many password guesses
- Password spraying → few common passwords against many accounts
- Credential stuffing → leaked credentials tested on another service

## Evidence
Look for:
- Repeated failed logins
- Same IP making many attempts
- Same account being targeted
- Successful login after multiple failures

## Kali Practical

### 1. Check SSH
```bash
sudo systemctl status ssh

. Start SSH if needed
sudo sFind Kali IP
ip a

Example:

10.0.2.15
4. Connect through SSH
ssh kali@10.0.2.15
5. Check SSH logs
sudo journalctl -u ssh
6. Check recent SSH events
sudo journalctl -u ssh --since "10 minutes ago"
Lab Observation

After entering an incorrect password, the log showed:

Failed password for kali from 10.0.2.15

A successful login produced:

Accepted password for kali from 10.0.2.15
SOC Investigation

Ask:

What IP is making the attempts?
Which account is being targeted?
How many failures occurred?
How quickly did they happen?
Did a successful login occur afterward?
What did the account do after logging in?

Example:

Failed
Failed
Failed
Failed
SUCCESS

This deserves further investigation because the account may have been compromised.

Prevention
MFA
Strong passwords
Rate limiting
Login delays/lockouts
Authentication monitoring
Key Takeaway

Brute force = repeated credential attempts.

SOC focus:
IP + account + time + number of attempts + successful login afterward



### And we'll do this for every topic


For **port scanning**, you'll have commands like:


```bash
nmap 10.0.2.15
sudo journalctl --since "5 minutes ago"
systemctl start ssh

For web attacks, you'll have:

python3 -m http.server 8000
curl http://127.0.0.1:8000


