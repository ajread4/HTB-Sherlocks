# Operation Blackout 2025: Phantom Check

Hack the Box Link: [Operation Blackout 2025: Phantom Check](https://app.hackthebox.com/sherlocks/Operation%2520Blackout%25202025%253A%2520Phantom%2520Check?tab=play_sherlock)


1. Which WMI class did the attacker use to retrieve model and manufacturer information for virtualization detection?
*Answer*:Win32_ComputerSystem
* Pull the event logs from Microsoft-Windows-Powershell.evtx and identify execution within Event ID 800. Filter the logs for "WMI" on the payloads and find a log with ```{"EventData":{"Data":"$Manufacturer = Get-WmiObject -Class Win32_ComputerSystem | select-object -expandproperty \"Manufacturer\ ..." ```, which contains the class in the first line. Execution occured at 2025-04-09 09:19:10. 

2. Which WMI query did the attacker execute to retrieve the current temperature value of the machine?
*Answer*: SELECT * FROM MSAcpi_ThermalZoneTemperature
* Pull the event logs from Microsoft-Windows-Powershell.evtx and identify execution within Event ID 800. Filter the logs for "WMI" based on payloads and find the activity within ```Get-WmiObject -Query \"SELECT * FROM MSAcpi_ThermalZoneTemperature``` at 2025-04-09 09:20:12. This activity can also be found with event ID 4104 within Windows-Powershell-Operational.evtx logs. 

3. The attacker loaded a PowerShell script to detect virtualization. What is the function name of the script?
*Answer*: Check-VM
* Pull event logs for Windows-Powershell-Operational.evtx and focus on Event ID 4104. Locate the script by looking at the payloads and searching for VM or execution around the time of the previous activity in above questions. The function can be found with ```ScriptBlockText: function Check-VM, {, &lt;# , .SYNOPSIS , Nishang script which detects whether it is in a known virtual machine.,  , .DESCRIPTION , This script uses known parameters or 'fingerprints' of Hyper-V, VMWare, Virtual PC, Virtual Box,, Xen and QEMU for detecting the environment., .EXAMPLE , PS &gt; Check-VM,  , .LINK , http://www.labofapenetrationtester.com/2013/01/quick-post-check-if-your-payload-is.html, https://github.com/samratashok/nishang, .NOTES , The script draws heavily from checkvm.rb post module from msf.,```. Activity occurred at 2025-04-09 09:20:53. 

4. Which registry key did the above script query to retrieve service details for virtualization detection?
*Answer*: HKLM:\SYSTEM\ControlSet001\Services
* Within the eventid 4104 identified in question 3, you can find the full script that determines the service details. The line within the script looks like ``` $xen = Get-ChildItem HKLM:\SYSTEM\ControlSet001\Services,``` or ```if (!$vbvm),         {,             $vb = Get-ChildItem HKLM:\SYSTEM\ControlSet001\Services,```. The script executes it a few times since it needs to keep grabbing and comparing service details for each type of virtualization. 

5. The VM detection script can also identify VirtualBox. Which processes is it comparing to determine if the system is running VirtualBox?
*Answer*: vboxservice.exe, vboxtray.exe
* Within the eventid 4104 identified in question 3, you can find the full script that determines the type of virtualization. Within the script, there is the line ```if (($vb -eq "vboxservice.exe") -or ($vb -match "vboxtray.exe"))``` to check to see if those two processes are running on the host. Those two processes are unique for VirtualBox. 

6. The VM detection script prints any detection with the prefix 'This is a'. Which two virtualization platforms did the script detect?
*Answer*: Hyper-V, Vmware
* Pull event logs for Windows-Powershell-Operational.evtx and look for event ID 4103. Within the 4103 logs, there are logs for Command Invocation that show what is printed out for the Check-VM script. The log looks like ```CommandInvocation(Out-Default): "Out-Default", ParameterBinding(Out-Default): name="InputObject"; value="This is a Hyper-V machine.", ParameterBinding(Out-Default): name="InputObject"; value="This is a VMWare machine.", ```. It occurred at 2025-04-09 09:20:57. 
