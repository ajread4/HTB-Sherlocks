# Heartbreaker

Hack the Box Link: [HeartBreaker]()

1. The victim received an email from an unidentified sender. What email address was used for the suspicious email?
* *Answer*:ImSecretlyYours@proton[.]me
* Look through the output of pff-tools to find the InternetHeaders.txt file which contains the sender email address. Command to run pfftools is ```pffexport -f all -m all ashwilliams012100@gmail.com.ost ```. 
2. It appears there's a link within the email. Can you provide the complete URL where the malicious binary file was hosted?
* *Answer*:http[:]//44[.]206[.]187[.]144[:]9000/Superstar_MemberCard.tiff.exe
* Utilize pff-tools to search through the email messages and find the message with the malicious link. The .ost file with the email inbox is within the User\AppData\Local\Microsoft\Outlook folder. 
3. The threat actor managed to identify the victim's AWS credentials. From which file type did the threat actor extract these credentials?
* *Answer*: .ost
* It was located within a draft email that I had discovered using the above steps with ```pffexport```. 
4. Provide the actual IAM credentials of the victim found within the artifacts.
* *Answer*: [Removed AKIA]
* If you search for "AKIA" with a recursive grep, it will show up in the forensic image. Can render HTML with https://htmledit.squarefree.com/. 
5. When (UTC) was the malicious binary activated on the victim's workstation?
* *Answer*: 2024-03-13 10:45:02
* Utilize prefetch file data to find when it was executed with ```python3 prefetcher.py -f ~/Downloads/HeartBreaker/wb-ws-01/C/Windows/prefetch/SUPERSTAR_MEMBERCARD.TIFF.EXE-C2488B05.pf```. 
6. Following the download and execution of the binary file, the victim attempted to search for specific keywords on the internet. What were those keywords?
* *Answer*: superstar cafe membership
* Utilize ```browser-history``` with ```browser-history -b Firefox -p /home/ajread/Downloads/HeartBreaker/wb-ws-01/C/Users/ash.williams/AppData/Roaming/Mozilla/Firefox/Profiles/hy42b1gc.default-release/``` to find the searches post download. 
7. At what time (UTC) did the binary successfully send an identical malicious email from the victim's machine to all the contacts?
* *Answer*: 2024-03-13 10:47:51
* Look through the messages in ```/wb-ws-01/C/Users/ash.williams/AppData/Local/Microsoft/Outlook/ashwilliams012100@gmail.com.ost.export/Root - Mailbox/IPM_SUBTREE/[Gmail]/Sent Mail/Message00005``` and find the one with the large number of recipients and search through the internet header information to find the time. 
8. How many recipients were targeted by the distribution of the said email excluding the victim's email account?
* *Answer*: 58
* Look through the recipients list in ```/wb-ws-01/C/Users/ash.williams/AppData/Local/Microsoft/Outlook/ashwilliams012100@gmail.com.ost.export/Root - Mailbox/IPM_SUBTREE/[Gmail]/Sent Mail/Message00005```
9. Which legitimate program was utilized to obtain details regarding the domain controller?
* *Answer*: nltest.exe
* Use [chainsaw](https://github.com/WithSecureLabs/chainsaw/tree/master) to search for the superstart_membercard.tiff.exe execution within sysmon logs with ```~/tools/chainsaw/target/release/chainsaw search --skip-errors "Superstar_MemberCard.tiff.exe" wb-ws-01/C/```. 
10. Specify the domain (including sub-domain if applicable) that was used to download the tool for exfiltration.
* *Answer*: us.softradar.com
* Look for Sysmon Event ID 22 for DNS events that show possible downloads set up. This came after execution in Sysmon of common zipping tools like WinSCP. 
11. The threat actor attempted to conceal the tool to elude suspicion. Can you specify the name of the folder used to store and hide the file transfer program?
* *Answer*: HelpDesk-Tools
* Look for Sysmon Event ID 11 for File Creation, occurs right after the zipping and download of exfil tools occur. 
12. Under which MITRE ATT&CK technique does the action described in question #11 fall?
* *Answer*: Masquerading
* Within the Sysmon event log
13. Can you determine the minimum number of files that were compressed before they were extracted?
* *Answer*: 26
* Look through Chainsaw output for TargetFileNames with the Image as ```C:\Users\ash.williams\Downloads\Superstar_MemberCard.tiff.exe```. 
14. To exfiltrate data from the victim's workstation, the binary executed a command. Can you provide the complete command used for this action?
* *Answer*: "C:\Users\Public\HelpDesk-Tools\WinSCP.com" /script="C:\Users\Public\HelpDesk-Tools\maintenanceScript.txt"
* Use [chainsaw](https://github.com/WithSecureLabs/chainsaw/tree/master) to search for HelpDesk-Tools with ```~/tools/chainsaw/target/release/chainsaw search --skip-errors "HelpDesk-Tools" wb-ws-01/C/```. 