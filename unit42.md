# Unit 42

Hack the Box Link: [Unit42](https://app.hackthebox.com/sherlocks/Unit42/play)

For pulling event logs, utilize [python-evtx](https://github.com/williballenthin/python-evtx)

1. How many Event logs are there with Event ID 11?
* *Answer*: 56
* Filter the event logs for events with that event ID 
2. Whenever a process is created in memory, an event with Event ID 1 is recorded with details such as command line, hashes, process path, parent process path, etc. This information is very useful for an analyst because it allows us to see all programs executed on a system, which means we can spot any malicious processes being executed. What is the malicious process that infected the victim's system?
* *Answer*: C:\Users\CyberJunkie\Downloads\Preventivo24.02.14.exe.exe
* Look at Sysmon Event ID 1s and identify the suspicious file name as well as the correspond MSIexec executions 
3. Which Cloud drive was used to distribute the malware?
* *Answer*: Dropbox
* Looking at Sysmon Event ID 22 for domain query and results that related directly to firefox traffic as well 
4. The initial malicious file time-stamped (a defense evasion technique, where the file creation date is changed to make it appear old) many files it created on disk. What was the timestamp changed to for a PDF file?
* *Answer*: 2024-01-14 08:10:06
* Look for Sysmon Event ID 2 to find timestomping completed by the adversary
5. The malicious file dropped a few files on disk. Where was "once.cmd" created on disk? Please answer with the full path along with the filename.
* *Answer*: C:\Users\CyberJunkie\AppData\Roaming\Photo and Fax Vn\Photo and vn 1.1.2\install\F97891C\WindowsVolume\Games\once.cmd
* Utilized jq to find the location of the file with grep as well: ```jq 'map(select(.EventID == "11"))' unit42.json | grep -C 10  "once.cmd"```
6. The malicious file attempted to reach a dummy domain, most likely to check the internet connection status. What domain name did it try to connect to?
* *Answer*: www[.]example[.]com
* Look for Sysmon event ID 22 to find the domain query 
7. Which IP address did the malicious process try to reach out to?
* *Answer*: 93.184.216.34
* Look for Sysmon event ID 3 and using the following jq: ```jq 'map(select(.ProcessId == "10672"))' unit42.json```
8. The malicious process terminated itself after infecting the PC with a backdoored variant of UltraVNC. When did the process terminate itself?
* *Answer*: 2024-02-14 03:41:58
* Look for Sysmon event ID 5 and use the folling: ```jq 'map(select(.EventID == "5"))' unit42.json```
