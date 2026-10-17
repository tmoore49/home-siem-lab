# MSSQL Password Spraying Investigation in Splunk
 ## Objective
 Re-investigate MSSQL Failed Logon logs from my first  project using Splunk,a SIEM, to find anything grep missed and confirm the scope of the attack 

 ## Environment and setup
 - Converted the .evtx file to XML on my chromebook (Linux) with evtx_dump.py
 - Uploaded MSSQL.xml to Splunk cloud free trial
 - Created a custom source type,mssql_evtx_xml, with LINE_BREAKER set to split each `<event>` into its own event

<img width="1902" height="1017" alt="01 xml file" src="https://github.com/user-attachments/assets/120c23bb-b1ec-4b7a-a8ed-5a4e60e9c14d" />



## Investigation 

### 1 All events
`sourcetype="mssql_evtx_xml"`
11 events 10 failed logins plus XML header 

<img width="1905" height="911" alt="02-systemtime" src="https://github.com/user-attachments/assets/0b140850-4396-4851-bfee-5052d4b8f8be" />


### 2. Timing

<img width="1905" height="911" alt="02-systemtime" src="https://github.com/user-attachments/assets/d8156752-a279-499c-8ae6-25e9d91dddd4" />

All Attempts happened at 13:46:01 on Nov 4,2019 within one second.That means it was an automated tool no human could type 10 logins in one second.
