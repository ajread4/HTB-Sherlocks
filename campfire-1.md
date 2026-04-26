# Campfire-1

Hack the Box Link: [Campfire](https://app.hackthebox.com/sherlocks/Campfire-1)

1. Analyzing Domain Controller Security Logs, can you confirm the date & time when the kerberoasting activity occurred?
* *Answer*: 2024-05-21 03:18:09
* Look for event ID 4769 within the Security logs and identify based on username not being DC01$, the encryption type of 0x17 
2. What is the Service Name that was targeted?
* *Answer*: MSSQLService
* Look within the Event ID 4769 logs to find the target service name with suspicious username not DC01$ and encryption type 0x17
3. It is really important to identify the Workstation from which this activity occurred. What is the IP Address of the workstation?
* *Answer*: 172.17.79.129
* Look within the Event ID 4769 logs to find the source ip with suspicious username not DC01$ and encryption type 0x17
4. Now that we have identified the workstation, a triage including PowerShell logs and Prefetch files are provided to you for some deeper insights so we can understand how this activity occurred on the endpoint. What is the name of the file used to Enumerate Active directory objects and possibly find Kerberoastable accounts in the network?
* *Answer*: powerview.ps1
* Utilize the PowerShell-Operational.evtx to find powershell executions, event ID 4104 is all the script blocks logged in powershell 
5. When was this script executed?
* *Answer*: 2024-05-21 03:16:32
* From the Event ID 4104 within PowerShell-Operational.evtx event logs 
6. What is the full path of the tool used to perform the actual kerberoasting attack?
* *Answer*: C:\USERS\ALONZO.SPIRE\DOWNLOADS\RUBEUS.EXE
* Utilize [w10pf_parse.py](https://github.com/DavidCruciani/tools/blob/master/win10_prefetch/w10pf_parse.py) to grab the times of the pf creations, look for around the time that we have script execution. Go to file downloads and find Rubeus download time as well. 
7. When was the tool executed to dump credentials?
* *Answer*: 2024-05-21 03:18:08
* From prefetch file output, returns times using [w10pf_parse.py](https://github.com/DavidCruciani/tools/blob/master/win10_prefetch/w10pf_parse.py)

