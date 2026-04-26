# Logjammer

Hack the Box Link: [Logjammer](https://app.hackthebox.com/sherlocks/Logjammer)

1. When did the cyberjunkie user first successfully log into his computer? (UTC)
* *Answer*: 2023-03-27 14:37:09.879890+00:00
* Look through the Security Event logs for event id 4624, which are for logon events. Filter with ```cat Security.json | jq '.[] | select(.EventID=="4624") | select(.TargetUserName=="CyberJunkie")'```. 
2. The user tampered with firewall settings on the system. Analyze the firewall event logs to find out the Name of the firewall rule added?
* *Answer*: Metasploit C2 Bypass
* Look for rules added after the user logged in and with a weird rule name: ```cat WindowsFW.json | jq '.[] | select(.TimeCreated>= "2023-03-27 14:37:09") | .RuleName'```. Could also search based on the modifying user SID within the "ModifyingUser" field. 
3. Whats the direction of the firewall rule?
* *Answer*: Outbound
* Based on the output of ```cat WindowsFW.json | jq '.[] | select(.RuleName== "Metasploit C2 Bypass")'```. 
4. The user changed audit policy of the computer. Whats the Subcategory of this changed policy?
* *Answer*: Other Object Access Events 
* Look for the specific event with ```cat Security.json | jq '.[] | select(.EventID=="4719")```. Need to look up the specific "SubcategoryId" here: https://github.com/Azure/Azure-Sentinel/blob/master/Tools/ParameterizedFunction/AuditEventDataLookup_Func.ps1. I couldnt find a list anywhere else. 
5. The user "cyberjunkie" created a scheduled task. Whats the name of this task?
* *Answer*: HTB-AUTOMATION
* Find with ```cat Security.json | jq '.[] | select(.EventID=="4698")'```.
6. hats the full path of the file which was scheduled for the task?
* *Answer*: C:\Users\CyberJunkie\Desktop\Automation-HTB.ps1
* Look through the TaskContent from ```cat Security.json | jq '.[] | select(.EventID=="4698")'```.
7. What are the arguments of the command?
* *Answer*: -A cyberjunkie@hackthebox[.]eu
* Look through the TaskContent from ```cat Security.json | jq '.[] | select(.EventID=="4698")'```.
8. The antivirus running on the system identified a threat and performed actions on it. Which tool was identified as malware by antivirus?
* *Answer*: Sharphound
* Brief look at the logs within Windows Defender with ```cat Defender.json | jq '.[] | select(."Detection User"=="DESKTOP-887GK2L\\CyberJunkie") | ."Threat Name"'```. 
9. Whats the full path of the malware which raised the alert?
* *Answer*: C:\Users\CyberJunkie\Downloads\SharpHound-v1.1.0.zip
* Brief look at the logs within Windows Defender with ```cat Defender.json | jq '.[] | select(."Detection User"=="DESKTOP-887GK2L\\CyberJunkie")'```. 
10. What action was taken by the antivirus?
* *Answer*: Quarantine
* Look through the Defender logs with ```cat Defender.json | jq '.[] | select(."Detection User"=="DESKTOP-887GK2L\\CyberJunkie") | ."Action Name"'```. 
11. The user used Powershell to execute commands. What command was executed by the user?
* *Answer*: Get-FileHash -Algorithm md5 .\Desktop\Automation-HTB.ps1
* Pull the command line with ```cat PowerShell.json | jq '.[] | select(.EventID=="4104") | select(.TimeCreated>="2023-03-27 14:37:09")'```
12. We suspect the user deleted some event logs. Which Event log file was cleared?
* *Answer*: Microsoft-Windows-Windows Firewall With Advanced Security/Firewall
* Look for system event logs with event id 104 afer the bad user logged in with ```cat System.json | jq '.[] | select(.EventID=="104") | select(.TimeCreated>="2023-03-27 14:37:09")'```. 