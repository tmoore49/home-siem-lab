# MSSQL Password Spraying Investigation in Splunk
 ## Objective
 Re-investigate MSSQL Failed Logon logs from my first  project using Splunk,a SIEM, to find anything grep missed and confirm the scope of the attack 

 ## Environment and setup
 - Converted the .evtx file to XML on my chromebook (Linux) with evtx_dump.py
 - Uploaded MSSQL.xml to Splunk cloud free trial
 - Created a custom source type,mssql_evtx_xml, with LINE_BREAKER set to split each `<event>` into its own event

<img width="1902" height="1017" alt="01 xml file" src="https://github.com/user-attachments/assets/120c23bb-b1ec-4b7a-a8ed-5a4e60e9c14d" />



## Investigation 

### 1. All events
`sourcetype="mssql_evtx_xml"`
11 events 10 failed logins plus XML header 

<img width="1918" height="887" alt="02-all-events" src="https://github.com/user-attachments/assets/e4434ec2-9fa0-4a34-847d-b87cd4363bf5" />



### 2. Timing

<img width="1905" height="911" alt="02-systemtime" src="https://github.com/user-attachments/assets/d8156752-a279-499c-8ae6-25e9d91dddd4" />

All Attempts happened at 13:46:01 on Nov 4,2019 within one second.That means it was an automated tool no human could type 10 logins in one second.

### 3. No successful logins
`sourcetype="mssql_evtx_xml" 18454`

Event ID 18454 means a successful login. Zero result,so none of the 18456 failed logins got through.

<img width="1708" height="849" alt="02-no-success" src="https://github.com/user-attachments/assets/f5690ce5-30e0-4db2-b7f7-0c9473655af5" />

### 4. Attacker source
`sourcetype="mssql_evtx_xml" 10.0.2.17`

All 10 failed logins came from one source IP 10.0.2.17,targeting the SQL server MSEDGEWIN10

<img width="1897" height="920" alt="02 attacker  tip" src="https://github.com/user-attachments/assets/1a108e07-e08f-4ee8-8aaa-890ba207e012" />

### 5. targeted accounts 
`sourcetype="mssql_evtx_xml" 10.0.2.17 | rex "Data>&lt;string&gt;(?<account>[^&]+)" | stats count by account`

The attacker targeted 10 accounts with one attempt each, including the admin accounts sa and root and built-in SQL Server accounts. One attempt per account is the classic password Spraying pattern.

<img width="1890" height="907" alt="moneyshot" src="https://github.com/user-attachments/assets/40445c6b-6f02-48ce-afe7-dd9126700bdb" />

## Findings 
- Password Spraying attack from 10.0.2.17 against the SQL Server on MSEDGEWIN10
- 10 accounts targeted,one attempt each
- All attempts within one second so,automated
- Zero successful logins(no 18454 events),no compromise

## Grep vs Splunk 
Grep just works line by line, and the account names sat on a different line from the IP, so grepping 10.0.2.17 never showed them. Splunk treats each whole event as one unit,so the IP and account show up together.
