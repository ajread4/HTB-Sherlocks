# Detroit becomes Human

Hack the Box Link: [DetroitbecomesHuman](https://app.hackthebox.com/sherlocks/Detroit%20becomes%20Human/play)

1. What is the full link of a social media post which is part of the malware campaign, and was unknowingly opened by Alonzo spire?
* *Answer*: https[:]//www[.]facebook[.]com/AI.ultra.new/posts/pfbid0BqpxXypMtY5dWGy2GDfpRD4cQRppdNEC9SSa72FmPVKqik9iWNa2mRkpx9xziAS1l
* Found within the urls table of the History sqlite3 database for Microsoft Edge. Command to use is ```select * from urls```. 
2. Can you confirm the timestamp in UTC when alonzo visited this post?
* *Answer*: 2024–03–19 04:30:00
* The time can be found within the History sqlite3 database for Microsoft Edge. It is a Webkit timestamp that can be converted with:https://www.epochconverter.com/webkit Command to use to find the timestamp is ```select * from downloads``` and it is in one of the columns. 
3. Alonzo downloaded a file on the system thinking it was an AI Assistant tool. What is name of the archive file downloaded?
* *Answer*: AI.Gemini Ultra For PC V1.0.1.rar
* Found within the downloads table of the History sqllite3 database for Microsoft Edge. Command to use is ```select * from downloads```. 
4. What was the full direct url from where the file was downloaded?
* *Answer*: https[:]//drive[.]usercontent[.]google.com/download?id=1z-SGnYJCPE0HA_Faz6N7mD5qf0E-A76H&export=download
* Found within the downloads_url_chains table of the History sqllite3 database for Microsoft Edge. Command to use is ```select * from downloads_url_chains```. 
5. Alonzo then proceeded to install the newly download app, thinking that its a legit AI tool. What is the true product version which was installed?
* *Answer*: 3.32.3
* Look at the Application event logs for event ID 1033 and MSI installers. The data section within the event log contains the version of the software that was installed. 
6. When was the malicious product/package successfully installed on the system?
* *Answer*: 2024-03-19 04:31:33
* Look within the $MFT using MFTECmd tool and find the Last Accessed time. Or, look at the Application event logs for event ID 1033 and MSI installers. The TimeCreated for the log is the time of install. 
7. The malware used a legitimate location to stage its file on the endpoint. Can you find out the Directory path of this location?
* *Answer*: C:\Program Files (x86)\Google
* Look within the powershell log directly after the time when the malware was install on the system to find a suspicious name and powershell command with execution bypass. 
8. The malware executed a command from a file. What is name of this file?
* *Answer*: install.cmd
* Look within the MFT using MFTECmd tool for files within the same directory of the ru.ps1 from earlier answer. 
9. What are the contents of the file from question 8? Remove whitespace to avoid format issues.
* *Answer*: ```@echo off powershell -ExecutionPolicy Bypass -File "%~dp0nmmhkkegccagdldgiimedpic/ru.ps1"```
* Use [MFTExplorer](https://ericzimmerman.github.io/#!index.md) to examine the contents of the files within the $MFT. Or, you can use MFTECmd with ```./MFTECmd -f ~/Downloads/detroitbecomehuman/Triage/C/\$MFT --de 51471```.  
10. What was the command executed from this file according to the logs?
* *Answer*: ```powershell -ExecutionPolicy Bypass -File C:\Program Files (x86)\Google\Install\nmmhkkegccagdldgiimedpic/ru.ps1```
* From the Powershell logs, look for ```ru.ps1``` and find the command in powershell that was run with the file. 
11. Under malware staging Directory, a js file resides which is very small in size. What is the hex offset for this file on the filesystem?
* *Answer*: 3E90C00
* Look within MFTECmd to find the js by ctf+F for ```\Google\Install\nmmhkkegccagdldgiimedpic```. The file is "content.js". You can also find using ```./MFTECmd -f ~/Downloads/detroitbecomehuman/Triage/C/\$MFT --de 64067``` to output detailed information. 
12. Recover the contents of this js file so we can forward this to our RE/MA team for further analysis and understanding of this infection chain. To sanitize the payload, remove whitespaces.
* *Answer*: ```varisContentScriptExecuted=localStorage.getItem('contentScriptExecuted');if(!isContentScriptExecuted){chrome.runtime.sendMessage({action:'executeFunction'},function(response){localStorage.setItem('contentscriptExecuted',true);});}```
* Use [MFTExplorer](https://ericzimmerman.github.io/#!index.md) to examine the contents of the files within the $MFT. Or, use ```bless``` to parse the $MFT and navigate to the offset of the file using the above answer. Or, you can use MFTECmd with ```./MFTECmd -f ~/Downloads/detroitbecomehuman/Triage/C/\$MFT --de 64067```. 
13. Upon seeing no AI Assistant app being run, alonzo tried searching it from file explorer. What keywords did he use to search?
* *Answer*: Google Ai Gemini tool 
* Use Regripper and the Word Wheel Query to find the solution, command to run is ```regripper -r NTUSER.DAT -a```.
14. When did alonzo searched it?
* *Answer*: 2024-03-19 04:32:11
* From the output of regripper within the Word Wheel Query section using  ```regripper -r NTUSER.DAT -a```.
15. After alonzo could not find any AI tool on the system, he became suspicious, contacted the security team and deleted the downloaded file. When was the file deleted by alonzo?
* *Answer*: 2024-03-19 04:34:16
* Use MFTECmd to find one of the files in the recycle bin references the Gemini explorer with ```./MFTECmd -f ~/Downloads/detroitbecomehuman/Triage/C/\$MFT --de 462452```. Use the standard information creation time. 
16. Looking back at the starting point of this infection, please find the md5 hash of the malicious installer.
* *Answer*: BF17D7F8DAC7DF58B37582CEC39E609D
* Look up the file in Virustotal. 
