Splunk — Basics
What is Splunk?

Splunk is a platform used to collect, search, analyze, monitor, and visualize machine-generated data/logs.

In cybersecurity, it can be used as a SIEM to investigate security events and detect suspicious activity.

Core Concepts
1. Event

An event is an individual piece of log/data.

Example:

User: admin
Source IP: 10.0.0.5
Action: Failed Login
Time: 23:10
2. Index

An index is where Splunk stores and organizes data.

index=windows

Searches data in the windows index.

index=*

Searches across all accessible indexes.

3. Fields

Fields are individual pieces of information extracted from events.

Examples:

host
source
sourcetype
user
src_ip
dest_ip
status
_time
4. SPL

SPL = Search Processing Language

It is the language used to search and process Splunk data.

Example:

index=* | head 20
5. Pipe |

The pipe passes the output of one command to another.

index=* | stats count

Meaning:

Search data → Count results
Important Fields
Field	Meaning
_time	When the event occurred
host	Machine that generated the event
source	Where the data came from
sourcetype	Type/format of the data

Remember:

source = where the data came from
sourcetype = what type of data it is
Important SPL Commands
Command	Purpose
search	Search/filter events
head	Show first results
stats	Calculate/group data
table	Display selected fields
fields	Include/remove fields
sort	Sort results
dedup	Remove duplicates
where	Filter results
eval	Create/modify fields
rename	Rename fields
timechart	Analyze data over time
Examples
Search everything
index=*
Show first 20 events
index=* | head 20
Count events
index=* | stats count
Count events by IP
index=* | stats count by src_ip
Display specific fields
index=* | table _time host user src_ip
Time Range

Splunk searches are affected by the selected time range.

Common options:

Last 15 minutes
Last 1 hour
Last 24 hours
Last 7 days
Custom

Always check the time picker when a search appears to return nothing.

Splunk SOC Workflow
Logs
 ↓
Splunk
 ↓
Search with SPL
 ↓
Filter events
 ↓
Analyze fields
 ↓
Find suspicious activity
 ↓
Investigate
 ↓
Alert / Detection
Key Things to Remember
Event      → Individual log record
Index      → Data storage/organization
Field      → Information inside an event
SPL        → Splunk search language
Pipe |     → Pass results to another command
_time      → Event timestamp
