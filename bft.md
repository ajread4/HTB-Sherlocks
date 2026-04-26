# BFT

Hack the Box Link: [BFT](https://app.hackthebox.com/sherlocks/BFT)

1. Simon Stark was targeted by attackers on February 13. He downloaded a ZIP file from a link received in an email. What was the name of the ZIP file he downloaded from the link?
* *Answer*: Stage-20240213T093324Z-001.zip
* Look through the MFT on linux using [analyzeMFT](https://github.com/rowingdude/analyzeMFT). Did a control+F for ".zip". 
2. Examine the Zone Identifier contents for the initially downloaded ZIP file. This field reveals the HostUrl from where the file was downloaded, serving as a valuable Indicator of Compromise (IOC) in our investigation/analysis. What is the full Host URL from where this ZIP file was downloaded?
* *Answer*: https[:]//storage[.]googleapis[.]com/drive-bulk-export-anonymous/20240213T093324.039Z/4133399871716478688/a40aecd0-1cf3-4f88-b55a-e188d5c1c04f/1/c277a8b4-afa9-4d34-b8ca-e1eb5e5f983c?authuser
* Look through the output of MFTECmd for the Zone Identifer section 
3. What is the full path and name of the malicious file that executed malicious code and connected to a C2 server?
* *Answer*: C:\Users\simon.stark\Downloads\Stage-20240213T093324Z-001\Stage\invoice\invoices\invoice.bat
* Look through the MFT data and find the unzipping of the Staged filed with the various nested invoice files 
4. Analyze the $Created0x30 timestamp for the previously identified file. When was this file created on disk?
* *Answer*: 2024-02-13 16:38:39.9341326
* Use the output of MFTECmd 
5. Finding the hex offset of an MFT record is beneficial in many investigative scenarios. Find the hex offset of the stager file from Question 3.
* *Answer*: 16E3000
* Since the entry number of the file is 23436 in the MFT, the offset is going to be 23436*1024 
6. Each MFT record is 1024 bytes in size. If a file on disk has smaller size than 1024 bytes, they can be stored directly on MFT File itself. These are called MFT Resident files. During Windows File system Investigation, its crucial to look for any malicious/suspicious files that may be resident in MFT. This way we can find contents of malicious files/scripts. Find the contents of The malicious stager identified in Question3 and answer with the C2 IP and port.
* *Answer*: 43.204.110.203:6666
* If you go to the offset, you can see the powershell command for the download