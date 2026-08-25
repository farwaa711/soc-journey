Splunk Level 1 — Simple Table
What you want to do	SPL	Meaning
🔍 Search all data	index=*	Show all available events
🔢 Count everything	index=* | stats count	Count all matching events
👀 Show first 5	index=* | head 5	Show only 5 events
🎯 Filter by field	index=* sourcetype=stash	Show only stash events
🎯 Filter by 2 things	index=* sourcetype=stash indicator="Installed apps"	Must match both conditions
📊 Count by field	index=* | stats count by sourcetype	Count events for each sourcetype
📊 Count by another field	index=* | stats count by indicator	Count events for each indicator
📋 Show specific fields	index=* | table _time host source
