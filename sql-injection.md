# SQL Injection (SQLi)

## Definition
SQL Injection is a web vulnerability where attacker-controlled input
is improperly included in a SQL query, allowing the input to influence
the query's intended logic.

## Attacker's Goal
Depending on the vulnerability, an attacker may try to:
- Bypass authentication
- Read database data
- Modify or delete data
- Discover database information
- Escalate access

## How It Works

User input
    ↓
Web application
    ↓
SQL query
    ↓
Database

Vulnerable application:

User input → SQL query without safe parameterization
                     ↓
              Query behavior changes

The root problem is usually unsafe handling of user input.

## Common Locations
SQLi can occur in:
- Login forms
- Search fields
- URL parameters
- POST parameters
- Cookies
- HTTP headers
- API parameters

## Types
- **In-band SQLi** → Data is returned through the same application channel.
- **Blind SQLi** → No useful data is directly returned; behavior/timing
  can reveal information.
- **Out-of-band SQLi** → The database causes information to be sent through
  another channel.

## Practical Lab — Juice Shop

### 1. Open Burp Suite
Go to:

Proxy → HTTP history

Find a request such as:

GET /rest/products/search?q= HTTP/1.1

### 2. Send to Repeater
Right-click the request:

Send to Repeater

### 3. Establish a normal baseline

Change the parameter to:

q=apple

Request:

GET /rest/products/search?q=apple HTTP/1.1

We received:

HTTP/1.1 200 OK

and product results.

### 4. Test unusual input

We tested:

q='

The server still returned:

HTTP/1.1 200 OK

## Important Lab Lesson

A `200 OK` response does NOT prove SQL Injection.

It only means the server successfully processed the HTTP request.

A single quote test by itself is not enough to conclude that
an endpoint is vulnerable.

## Evidence / Logs

Possible evidence includes:

- Suspicious input in HTTP parameters
- Repeated requests to the same endpoint
- SQL-related error messages
- Unusual HTTP responses
- Database/application errors
- Successful activity following exploitation attempts

Example suspicious request:

GET /search?q=<suspicious-input>

## Important SOC Fields

- **Timestamp** → When?
- **Source IP** → Who?
- **HTTP method** → GET/POST/etc.
- **URL** → Which endpoint?
- **Parameter** → Which input was manipulated?
- **User/session** → Which account?
- **Status code** → What response occurred?
- **Response size** → Did the response change?
- **Frequency** → How many attempts?
- **Application/database errors** → Did something unusual occur?

## Where to Look for Evidence

Depending on the environment:

- Web server access logs
- Application logs
- WAF logs
- Reverse-proxy logs
- Database logs
- SIEM

Important:

Not every application records enough information to detect SQLi
from its normal logs.

## SOC Investigation

1. Identify the source IP.
2. Identify the targeted endpoint.
3. Identify the manipulated parameter.
4. Check whether similar requests were repeated.
5. Examine HTTP status codes and responses.
6. Check application/database errors.
7. Check whether successful exploitation was followed by
   suspicious data access or other activity.
8. Determine whether the activity was legitimate testing or malicious.

## Detection

Possible detection signals:

- Repeated unusual input in parameters
- SQL syntax patterns in requests
- Multiple probes against the same endpoint
- Database errors following suspicious requests
- WAF alerts
- Unusual data returned by an endpoint

Detection should use multiple signals rather than treating one
character or one request as proof of SQL Injection.

## Prevention

- Use parameterized queries / prepared statements
- Use safe ORM/database APIs
- Validate input where appropriate
- Apply least-privilege database accounts
- Do not expose detailed database errors to users
- Use WAF as an additional security layer
- Monitor application and database activity

## Key Takeaway

**SQL Injection = attacker-controlled input influences a SQL query.**

Pentesting focus:
**Can the input change the intended database query?**

SOC focus:
**What suspicious requests, errors, responses, and follow-up
database activity can reveal the attack?**
