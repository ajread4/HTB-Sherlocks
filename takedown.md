# Takedown

Hack the Box Link: [Takedown](https://app.hackthebox.com/sherlocks/Takedown)

1. From what domain is the VBS script downloaded?
* *Answer*: escuelademarina[.]com
* Look within the SMBv2 traffic to find the downloaded script via SMB
2. What was the IP address associated with the domain in question #1 used for this attack?
* *Answer*: 165[.]22[.]16[.]55
* Look within the SMBv2 traffic requests and response to find the source and destination 
3. What is the filename of the VBS script used for initial access?
* *Answer*: AZURE_DOC_OPEN.vbs
* Look within the SMBv2 traffic to find the downloaded script via SMB
4. What was the URL used to get a PowerShell script?
* *Answer*: badbutperfect[.]com/nrwncpwo
* Find the information by following the TCP stream of the SMBv2 traffic 
5. What likely legit binary was downloaded to the victim machine?
* *Answer*: AutoHotKey.exe
* Find the reference within the downloaded file from question 4 by focusing on HTTP objects in wireshark 
6. From what URL was the malware used with the binary from question #5 downloaded?
* *Answer*: http[:]//badbutperfect[.]com/jvtobaqj
* Find the reference within the downloaded file from question 4 by focusing on HTTP objects in wireshark 
7. What filename was the malware from question #6 given on disk?
* *Answer*: script.ahk
* Based on the file contents from question 4, used wireshark to grab the file and read on the command line 
8. What is the TLSH of the malware?
* *Answer*: T15E430A36DBC5202AD8E3074270096562FE7DC0215B4B32659C9EF16835CF6FF9B6A1B8
* Look at the file that was downloaded and renamed to be script.ahk, remove the comments that are unecessary and run sha256sum. Paste the sha256 into Virustotal and find the TLSH
9. What is the name given to this malware? Use the name used by McAfee, Ikarus, and alejandro.sanchez.
* *Answer*: DarkGate
* Look at the virus total write up with that was run answering question 8 
10. What is the user-agent string of the infected machine?
* *Answer*: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/118.0.0.0 Safari/537.36
* Look for the POST requests to the badbutperfect[.]com url
11. To what IP does the RAT from the previous question connect?
* *Answer*: 103[.]124[.]105[.]78
* Wireshark displays the destination IP address of the POST requests. 