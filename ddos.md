# DDoS Detection & Investigation — SOC L1

> **Core idea:** DDoS = overwhelming a service with traffic/requests so legitimate users cannot access it normally.

---

## 1. What DDoS Actually Does

**Attacker → Huge / abnormal traffic → Application / Server / Network → Service degradation or outage**

Main targets:

* **Network:** bandwidth / packet-processing capacity
* **Transport:** TCP/UDP connection handling
* **Application:** HTTP/API resources, CPU, database, workers

### DDoS ≠ "many requests" alone

High traffic can be legitimate.

**The analyst's job is to determine whether the traffic is abnormal + malicious + causing impact.**

---

## 2. The SOC Mental Model

When investigating:

**TIME → VOLUME → SOURCE → TARGET → PATTERN → IMPACT**

Ask:

1. **When** did it start?
2. **How much** traffic increased?
3. **Where** is it coming from?
4. **What** endpoint/service is being targeted?
5. **What pattern** does the traffic follow?
6. **What impact** is the system experiencing?

---

## 3. Strong DDoS Indicators

### Traffic indicators

* Sudden traffic/request spike
* Large increase compared with baseline
* Many requests within a short period
* Large number of source IPs
* Repeated requests to the same endpoint
* Unusual geographic/source distribution
* Abnormal request rate per IP

### Application indicators

* Increased **4xx/5xx** responses
* Increased **429 Too Many Requests**
* Increased latency
* Requests exhausting application resources
* One endpoint receiving disproportionate traffic
* Legitimate users experiencing failures

### Infrastructure indicators

* CPU/memory exhaustion
* Network bandwidth saturation
* Connection/socket exhaustion
* Load balancer/CDN alerts
* Server health degradation

---

## 4. Important HTTP Status Codes

| Code            | Meaning                 | SOC relevance                                           |
| --------------- | ----------------------- | ------------------------------------------------------- |
| **200**         | Success                 | Request succeeded                                       |
| **403**         | Forbidden               | Access denied; can appear during blocking/rate limiting |
| **404**         | Not Found               | Repeated probing/scanning may be suspicious             |
| **429**         | Too Many Requests       | Strong rate-limit signal                                |
| **500**         | Internal Server Error   | Application may be struggling                           |
| **502/503/504** | Gateway/service failure | Possible service overload                               |

> **Important:** No single status code proves DDoS.

---

## 5. What to Look For in Logs

### Web access log

```text
TIME
SOURCE IP
HTTP METHOD
URI / ENDPOINT
STATUS CODE
USER-AGENT
RESPONSE SIZE
RESPONSE TIME
```

Example:

```text
10:31:01  203.0.113.10  GET /login  429
10:31:01  203.0.113.11  GET /login  429
10:31:01  203.0.113.12  GET /login  429
```

Look for:

* Same endpoint
* Very short time window
* Rapid repetition
* Many sources
* Increasing failures/rate limits

---

## 6. Endpoint = What?

**Endpoint = a specific destination/API route the client communicates with.**

Examples:

```text
/login
/api/products
/api/search
/checkout
/admin
```

In DDoS investigation:

> **Which endpoint is receiving abnormal traffic?**

Example:

```text
/api/search → 85% of requests
```

That is much more useful than simply saying:

> "The website has lots of traffic."

---

## 7. Baseline vs Attack

Always compare abnormal traffic with normal behavior.

### Normal

```text
09:00 → 1,000 requests/min
10:00 → 1,200 requests/min
11:00 → 1,100 requests/min
```

### Suspicious

```text
11:20 → 1,100 requests/min
11:21 → 1,300
11:22 → 8,000
11:23 → 45,000
```

**Sudden deviation from baseline + suspicious patterns + service impact = strong DDoS hypothesis.**

---

## 8. Source IP Investigation

Do NOT immediately conclude:

> "One IP = attacker."

Check:

* Number of unique IPs
* Requests per IP
* Geographic distribution
* ASN/provider
* Reputation
* Whether IPs belong to known cloud/CDN infrastructure
* Whether traffic is distributed or concentrated

### Important

**Source IP ≠ attacker identity.**

IPs can represent:

* NAT gateways
* Proxies
* VPNs
* Cloud infrastructure
* Bots
* Legitimate users

---

## 9. User-Agent Investigation

Look for:

* Identical unusual User-Agent strings
* Automated clients
* Missing/abnormal User-Agent
* Same User-Agent across many sources
* Sudden change from normal browser traffic

But:

> **User-Agent alone is weak evidence because attackers can spoof it.**

Use it as **supporting evidence**, not proof.

---

## 10. The Strongest Evidence

Think in layers:

### Weak

```text
Traffic increased.
```

### Better

```text
Traffic increased suddenly and targeted one endpoint.
```

### Strong

```text
Traffic increased sharply, came from many sources,
targeted the same endpoint, produced abnormal rate-limit/
error responses, and caused service degradation.
```

### Very strong

```text
Clear traffic anomaly
+ coordinated request pattern
+ infrastructure/application impact
+ correlation with security controls
```

---

## 11. Do Not Confuse DDoS With Legitimate Traffic

Possible legitimate causes:

* Flash sale
* Product launch
* Marketing campaign
* Viral content
* Breaking news
* Major event
* Software update
* Normal business peak

Therefore:

**Traffic spike ≠ DDoS**

Ask:

> "Is there a legitimate business explanation?"

---

## 12. DDoS Investigation Workflow — L1

```text
ALERT
  ↓
Validate the alert
  ↓
Check traffic baseline
  ↓
Identify affected service
  ↓
Identify targeted endpoint
  ↓
Check request volume/rate
  ↓
Analyze source IP distribution
  ↓
Analyze User-Agent/request pattern
  ↓
Check HTTP status codes
  ↓
Check CPU/network/application impact
  ↓
Check CDN/WAF/rate-limit alerts
  ↓
Look for legitimate business explanation
  ↓
Determine severity
  ↓
Escalate / follow IR playbook
```

---

## 13. Evidence Collection

Record:

```text
Start time:
Peak time:
Affected service:
Target endpoint:
Request rate:
Source IP count:
Top source IPs:
HTTP methods:
Status-code distribution:
User-Agent pattern:
Geographic distribution:
CPU/memory:
Network utilization:
Application latency:
WAF/CDN alerts:
Business explanation:
```

**Always preserve the time window.**

Example:

```text
10:30–10:45 UTC
```

This allows other teams to correlate:

* Firewall logs
* WAF logs
* Load balancer logs
* Application logs
* Network telemetry
* Monitoring alerts

---

## 14. L1 Analyst's Decision

### Likely benign

```text
Traffic spike
+
Known business event
+
Normal request pattern
+
No significant service degradation
```

### Suspicious

```text
Unexpected spike
+
Abnormal request pattern
+
Targeted endpoint
+
Rate limiting/errors
```

### Likely DDoS

```text
Major traffic anomaly
+
Coordinated/distributed behavior
+
Clear service impact
+
No convincing legitimate explanation
```

**Do not overstate confidence.**

Use:

> "Traffic is consistent with a possible application-layer DDoS."

rather than:

> "This is definitely a DDoS."

until evidence supports it.

---

## 15. What L1 Should NOT Do

Do not:

* Block IPs blindly
* Assume every traffic spike is malicious
* Treat one IP as proof of an attacker
* Treat 403/429 as proof of DDoS
* Ignore legitimate business events
* Investigate without a time window
* Make attribution based only on IP/User-Agent
* Change production controls without authorization

Follow the organization's **incident-response and escalation procedures**.

---

# Memory Formula

## **DDoS = SPIKE + PATTERN + TARGET + IMPACT**

### SPIKE

Is traffic abnormal compared with baseline?

### PATTERN

Does it look automated, repetitive, or coordinated?

### TARGET

What service/endpoint is being hit?

### IMPACT

Is the service actually degrading?

---

# Fast SOC Checklist

* [ ] Confirm alert/time
* [ ] Compare against baseline
* [ ] Identify affected service
* [ ] Identify endpoint
* [ ] Check request rate
* [ ] Count unique source IPs
* [ ] Check source distribution
* [ ] Check User-Agent
* [ ] Check HTTP status codes
* [ ] Check latency/errors
* [ ] Check CPU/network/application health
* [ ] Check WAF/CDN/rate-limit events
* [ ] Check legitimate business explanation
* [ ] Document evidence
* [ ] Escalate according to severity

---

# One-Line Career Reminder

> **A SOC analyst does not investigate DDoS by asking "Is traffic high?" — they ask "Is the traffic abnormal, what pattern does it follow, what is it targeting, and is it causing measurable impact?"**
