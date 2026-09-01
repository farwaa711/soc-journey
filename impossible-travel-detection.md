# Impossible Travel Detection

## What is Impossible Travel?

**Impossible Travel** is a detection technique used to identify suspicious login activity when the same user appears to log in from two geographically distant locations within an unrealistic amount of time.

### Example

```text
09:00 → Sara logs in from Lahore, Pakistan
09:30 → Sara logs in from New York, USA
```

The user appears to have traveled thousands of kilometers in only 30 minutes.

🚨 This is **possible impossible-travel activity** and should be investigated.

> Important: Impossible travel does **not** automatically mean the account is compromised.

---

# Why It Can Be Suspicious

Possible explanations include:

* Stolen username/password
* Stolen session or authentication token
* VPN or proxy usage
* Cloud-hosted login
* Incorrect IP geolocation
* Mobile network geolocation errors
* Shared accounts
* Legitimate travel

Therefore, the detection should generate a **security alert for investigation**, not immediately declare the account compromised.

---

# Data Required

A basic impossible-travel detection needs authentication events containing information such as:

| Field     | Purpose                |
| --------- | ---------------------- |
| `_time`   | Login time             |
| `user`    | Account that logged in |
| `src_ip`  | Source IP              |
| `city`    | Login location         |
| `country` | Login country          |

Example:

```text
_time                 user    src_ip          city
2026-09-01 09:00:00   sara    185.10.10.5     Lahore
2026-09-01 09:30:00   sara    91.20.20.5     New York
```

---

# Detection Logic

The basic detection process is:

```text
Authentication Logs
        ↓
Group events by user
        ↓
Sort logins by time
        ↓
Find previous login
        ↓
Calculate time difference
        ↓
Determine location/distance
        ↓
Compare distance + travel time
        ↓
Suspicious?
        ↓
🚨 Generate Alert
```

The important variables are:

```text
Same user
+
Different locations
+
Large distance
+
Very short time
=
Possible Impossible Travel
```

---

# Splunk Investigation

## Step 1 — View the Authentication Events

```spl
source="impossible_travel.csv"
| table _time user src_ip city country
```

This gives a basic view of the login activity.

---

# Step 2 — Sort Events

```spl
source="impossible_travel.csv"
| sort 0 user _time
| table _time user src_ip city country
```

### Why?

We need each user's login events in chronological order.

Example:

```text
09:00 → Sara → Lahore
09:30 → Sara → New York
14:00 → Sara → Lahore
```

---

# Step 3 — Find the Previous Login

Use `streamstats`:

```spl
source="impossible_travel.csv"
| sort 0 user _time
| streamstats current=f last(city) as previous_city last(_time) as previous_time by user
| table _time user city previous_city previous_time
```

### What does `streamstats` do?

It allows us to use information from previous events.

For Sara:

```text
Current login:  New York
Previous login: Lahore
```

This gives us the two locations we need to compare.

---

# Step 4 — Calculate Time Difference

```spl
source="impossible_travel.csv"
| sort 0 user _time
| streamstats current=f last(city) as previous_city last(_time) as previous_time by user
| eval time_difference=round((_time-previous_time)/60,2)
| table _time user city previous_city time_difference
```

### Important calculation

```spl
(_time - previous_time) / 60
```

`_time` is stored as a Unix timestamp.

Dividing by `60` converts seconds into minutes.

Example:

```text
09:00 → Lahore
09:30 → New York

Time difference = 30 minutes
```

---

# Step 5 — Add Distance

For a training dataset, we can manually assign approximate distances:

```spl
| eval distance_km=case(
    previous_city="Lahore" AND city="New York",11900,
    previous_city="New York" AND city="Lahore",11900,
    previous_city="Faisalabad" AND city="Lahore",120,
    previous_city="Lahore" AND city="Faisalabad",120,
    previous_city="London" AND city="Manchester",260,
    previous_city="Manchester" AND city="London",260,
    true(),0
)
```

### Example

```text
Lahore → New York
Distance ≈ 11,900 km
```

This is useful for learning the detection concept.

⚠️ **Production warning:** manually defining city pairs is not a scalable real-world solution. Production environments should use reliable IP geolocation/enrichment or authentication-provider location data.

---

# Step 6 — Create the Detection Condition

Example threshold:

```spl
| where distance_km >= 500 AND time_difference < 60
```

This means:

> Flag the login if the user appears to travel at least 500 km in less than 60 minutes.

Complete training query:

```spl
source="impossible_travel.csv"
| sort 0 user _time
| streamstats current=f last(city) as previous_city last(_time) as previous_time by user
| eval time_difference=round((_time-previous_time)/60,2)
| eval distance_km=case(
    previous_city="Lahore" AND city="New York",11900,
    previous_city="New York" AND city="Lahore",11900,
    previous_city="Faisalabad" AND city="Lahore",120,
    previous_city="Lahore" AND city="Faisalabad",120,
    previous_city="London" AND city="Manchester",260,
    previous_city="Manchester" AND city="London",260,
    true(),0
)
| where distance_km >= 500 AND time_difference < 60
| table _time user city previous_city time_difference distance_km
```

---

# Example Detection Result

```text
user    city       previous_city    time_difference    distance_km
sara    New York   Lahore            30                 11900
```

### Interpretation

```text
Sara
Lahore → New York
30 minutes
≈11,900 km
```

🚨 **Possible Impossible Travel**

---

# Important: Suspicious ≠ Confirmed Attack

An impossible-travel alert should be treated as an **investigation lead**.

An analyst should investigate:

* Was the user using a VPN?
* Was the login from a corporate proxy?
* Is the IP geolocation accurate?
* Was the account shared?
* Was there a legitimate business trip?
* Was MFA completed?
* Was the login successful?
* Were there password changes?
* Were suspicious actions performed after the login?
* Were multiple IP addresses used?
* Was a session/token stolen?

---

# False Positives

Impossible-travel detections can generate false positives.

Common causes:

### VPN

```text
User → VPN → Login
```

The authentication system may see the VPN server's location instead of the user's actual location.

### Proxy

A corporate proxy can make many users appear to originate from the same location.

### Cloud Services

Authentication traffic may originate from cloud infrastructure rather than the user's physical location.

### Incorrect Geolocation

IP-to-location databases are not always accurate.

### Shared Accounts

Multiple people using one account can naturally create geographically distant logins.

---

# Detection Engineering Lessons

A weak rule might be:

```text
Different city = suspicious
```

This is too broad.

A better rule considers:

```text
Same user
+
Previous location
+
Current location
+
Distance
+
Time difference
```

The stronger detection is:

```text
Large distance
+
Very short time
+
Same user
=
Higher-confidence alert
```

---

# Investigation Workflow

When an alert fires:

```text
1. Identify the user
        ↓
2. Identify current IP
        ↓
3. Identify previous IP
        ↓
4. Compare locations
        ↓
5. Calculate time difference
        ↓
6. Check VPN/proxy usage
        ↓
7. Check MFA
        ↓
8. Review other activity
        ↓
9. Decide whether it is benign or suspicious
```

---

# Detection vs Alert

A common mistake is thinking that a detection rule is just an alert.

They are different.

### Detection Logic

The SPL determines whether suspicious behavior exists.

```spl
| where distance_km >= 500 AND time_difference < 60
```

### Alert

The alert tells Splunk:

> "When this detection returns a result, notify the SOC."

Therefore:

```text
Detection Rule
      ↓
Search identifies suspicious behavior
      ↓
Alert triggers
      ↓
SOC Analyst investigates
```

---

# Key Splunk Commands Used

| Command       | Purpose                     |
| ------------- | --------------------------- |
| `sort`        | Orders events               |
| `streamstats` | Uses previous event values  |
| `eval`        | Creates/calculates fields   |
| `case()`      | Applies multiple conditions |
| `where`       | Filters results             |
| `table`       | Displays selected fields    |

---

# Key Concepts Learned

* Impossible Travel
* Authentication Logs
* IP Geolocation
* Previous Event Comparison
* `streamstats`
* Unix Timestamps
* Time Difference
* Detection Thresholds
* False Positives
* Detection Engineering
* Alerting
* SOC Investigation

---

# Detection Rule Summary

### Goal

Detect when the same user appears to travel an unrealistic distance in an unrealistic amount of time.

### Basic Logic

```text
Same user
    ↓
Compare current login with previous login
    ↓
Calculate time difference
    ↓
Determine distance
    ↓
Distance ≥ threshold?
    ↓
Time < threshold?
    ↓
🚨 Possible Impossible Travel
```

### Analyst Mindset

Never conclude:

> "Impossible travel = compromised account."

Instead conclude:

> **"This authentication pattern is inconsistent with normal physical travel and requires investigation."**
