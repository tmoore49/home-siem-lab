# MSSQL Password Spraying Investigation in Splunk
 ## Objective
 Re-investigate MSSQL Failed Logon logs from my first  project using Splunk,a SIEM, to find anything grep missed and confirm the scope of the attack 

 ## Environment and setup
 - Converted the .evtx file to XML on my chromebook (Linux) with evtx_dump.py
 - Uploaded MSSQL.xml to Splunk cloud free trial
 - Created a custom source type,mssql_evtx_xml, with LINE_BREAKER set to split each `<event>` into its own event

![XML file](01%20xml%20file.png)
