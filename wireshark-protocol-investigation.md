# Wireshark Protocol Investigation

## 🎯 Goal

Learn how to investigate common network protocols in Wireshark by identifying:

* Source IP
* Destination IP
* Source/Destination port
* Protocol
* Packet direction
* Request vs Response
* Important packet information

---

# 1. TCP 🔗

### Example

```text
10.0.2.15:52830 → 151.101.141.91:443 [SYN]
151.101.141.91:443 → 10.0.2.15:52830 [SYN, ACK]
10.0.2.15:52830 → 151.101.141.91:443 [ACK]
```

### Remember

TCP connection starts with the **3-way handshake**:

```text
SYN
 ↓
SYN, ACK
 ↓
ACK
```

* **Source IP** = sender of THIS packet
* **Destination IP** = receiver of THIS packet
* **52830** = temporary/ephemeral client port
* **443** = server HTTPS port

### Important

The IPs and ports **reverse direction** when the other side replies.

```text
Client → Server
Server → Client
```

### Connection ending

```text
FIN, ACK → normal connection closing
RST      → connection reset
```

⚠️ **RST does NOT automatically mean an attack.**

### Wireshark filter

```text
tcp
```

---

# 2. DNS 🌐

### Query

```text
10.0.2.15 → 192.168.100.1
Standard query A example.com
```

### Response

```text
192.168.100.1 → 10.0.2.15
Standard query response A example.com
A 172.66.147.243
A 104.20.23.154
```

### Remember

DNS is basically:

> **"What IP address belongs to this domain?"**

```text
Kali → DNS Server
"What IP is example.com?"

DNS Server → Kali
"These are its IPs."
```

### Important distinction

```text
192.168.100.1 = DNS server
172.66.147.243 = website/server IP
```

The DNS server is **not necessarily the website server**.

### `A` record

```text
A = IPv4 address
```

### Wireshark filter

```text
dns
```

---

# 3. HTTP 🌍

### Request

```text
10.0.2.15:46256 → 151.101.141.91:80
GET /success.txt?ipv4 HTTP/1.1
```

### Response

```text
151.101.141.91:80 → 10.0.2.15:46256
HTTP/1.1 200 OK
```

### Remember

HTTP uses:

```text
Port 80
```

The client uses a temporary port:

```text
46256 → 80
```

### Request

```text
Client → Server
GET
```

### Response

```text
Server → Client
200 OK
```

### `200 OK`

Means:

> The server successfully processed the request.

### Investigation idea

When you see HTTP, check:

* Source IP
* Destination IP
* URL/path
* HTTP method
* Status code
* Host/domain
* Request/response

### Wireshark filter

```text
http
```

---

# 4. TLS 🔐

### Client Hello

```text
10.0.2.15 → 151.101.141.91
Client Hello
SNI = firefox-settings-attachments.cdn.mozilla.net
TLS
```

### Server response

```text
151.101.141.91 → 10.0.2.15
Server Hello
Application Data
```

### Remember

TLS protects application traffic through encryption.

With HTTPS/TLS, you usually **cannot see**:

```text
HTTP body
Passwords
HTTP headers
Full URL/path
```

But you may still see:

```text
Source IP
Destination IP
Port 443
TLS handshake
SNI/domain
Packet sizes
Timing
Traffic patterns
```

### SNI

**SNI = Server Name Indication**

It can reveal the domain the client is trying to connect to.

Example:

```text
SNI = firefox-settings-attachments.cdn.mozilla.net
```

### Important

```text
443 = commonly HTTPS/TLS
```

⚠️ Port 443 alone does **not** mean traffic is malicious.

### Wireshark filter

```text
tls
```

---

# 5. ICMP 📡

### Echo Request

```text
10.0.2.15 → 8.8.8.8
Echo (ping) request
seq=1
```

### Echo Reply

```text
8.8.8.8 → 10.0.2.15
Echo (ping) reply
seq=1
```

### Remember

ICMP ping checks whether a host can respond.

```text
Client → Target
"Are you reachable?"

Target → Client
"Yes."
```

### Matching packets

The request and reply should have matching identifiers/sequence information.

```text
Request: seq=1
Reply:   seq=1
```

### `ttl`

TTL is an IP packet field that limits how many network hops a packet can survive.

For basic SOC investigation:

> **Notice it, but don't treat TTL alone as proof of anything malicious.**

### Wireshark filter

```text
icmp
```

---

# 🔍 The Most Important Rule

Whenever you look at **ANY packet**, ask:

```text
1. Who sent it?
   ↓
Source IP

2. Who received it?
   ↓
Destination IP

3. What service/protocol is involved?
   ↓
Protocol / Port

4. What is happening?
   ↓
Request / Response / Handshake / Data

5. Is the traffic normal for this system?
   ↓
Context + behavior
```

---

# 🔄 Request vs Response

Always remember that the direction changes:

### TCP

```text
Client → Server
SYN

Server → Client
SYN, ACK
```

### DNS

```text
Client → DNS Server
Query

DNS Server → Client
Response
```

### HTTP

```text
Client → Web Server
GET

Web Server → Client
200 OK
```

### ICMP

```text
Client → Target
Echo Request

Target → Client
Echo Reply
```

---

# 🧠 Protocol Quick Reference

| Protocol  | Common Port | Main Purpose         | Look For                            |
| --------- | ----------: | -------------------- | ----------------------------------- |
| TCP       |     Depends | Reliable connection  | SYN, ACK, FIN, RST                  |
| DNS       |          53 | Domain → IP lookup   | Query / Response                    |
| HTTP      |          80 | Web traffic          | GET, POST, 200 OK                   |
| TLS/HTTPS |         443 | Encrypted traffic    | Client Hello, SNI, Application Data |
| ICMP      |           — | Network reachability | Echo Request / Reply                |

---

# 🛡️ SOC Investigation Mindset

Don't think:

> "This packet looks suspicious."

Think:

```text
WHO?
Source IP

WHERE?
Destination IP

WHAT?
Protocol / Port

WHAT HAPPENED?
Request / Response / Connection

WHEN?
Timestamp

HOW OFTEN?
Traffic pattern

IS IT EXPECTED?
Compare with normal behavior
```

### Example

```text
10.0.2.15 → suspicious-domain.com
TCP/443
every 5 minutes
```

Possible concern:

> Repeated regular connections may indicate beaconing/C2.

But:

⚠️ **One suspicious-looking packet is not enough to prove compromise.**

Always investigate the **pattern + context**.
