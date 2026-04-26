# Reaper

Hack the Box Link: [Reaper](https://app.hackthebox.com/sherlocks/Reaper)

1. What is the IP Address for Forela-Wkstn001?
* *Answer*: 172.17.79.129
* Look through the NBNS packets for the refresh information, addr located in the additional information 
2. What is the IP Address for Forela-Wkstn002?
* *Answer*: 172.17.79.136
* Look through the NBNS packets for the refresh information, addr located in the additional information
3. What is the username of the account whose hash was stolen by attacker?
* *Answer*: arthur.kyle
* Look through the SMB2 traffic to find the username 
4. What is the IP Address of Unknown Device used by the attacker to intercept credentials?
* *Answer*: 172.17.79.135
* Look at the SMB2 traffic and see the NTLM negotiate with the new IP, looks to be an NTLM relay attack 
5. What was the fileshare navigated by the victim user account?
* *Answer*: ```\\DC01\Trip```
* View the SMB2 traffic and see the repeated failures to authenticate 
6. What is the source port used to logon to target workstation using the compromised account?
* *Answer*: 40252
* Search through the Security.evtx with ```python3 evtx_dump_json.py ~/Downloads/Reaper/Security.evtx | jq '.[] | select(.EventID=="4624" and .IpAddress=="172.17.79.135")'```
7. What is the Logon ID for the malicious session?
* *Answer*: 0x64A799
* Search through the Security.evtx with ```python3 evtx_dump_json.py ~/Downloads/Reaper/Security.evtx | jq '.[] | select(.EventID=="4624" and .IpAddress=="172.17.79.135")'```
8. The detection was based on the mismatch of hostname and the assigned IP Address.What is the workstation name and the source IP Address from which the malicious logon occur?
* *Answer*: FORELA-WKSTN002, 172.17.79.135
* Search through the Security.evtx with ```python3 evtx_dump_json.py ~/Downloads/Reaper/Security.evtx | jq '.[] | select(.EventID=="4624" and .IpAddress=="172.17.79.135")'```
9. At what UTC time did the the malicious logon happen?
* *Answer*: 2024-07-31 04:55:16
* Search through the Security.evtx with ```python3 evtx_dump_json.py ~/Downloads/Reaper/Security.evtx | jq '.[] | select(.EventID=="4624" and .IpAddress=="172.17.79.135")'```
10. What is the share Name accessed as part of the authentication process by the malicious tool used by the attacker?
* *Answer*: ```\\*IPC$```
* Search through the Security.evtx with ```python3 evtx_dump_json.py ~/Downloads/Reaper/Security.evtx | jq '.[] | select(.IpAddress=="172.17.79.135")'```