# Campfire-2

Hack the Box Link: [CampFire-2](https://app.hackthebox.com/sherlocks/Campfire-2)

1. When did the ASREP Roasting attack occur, and when did the attacker request the Kerberos ticket for the vulnerable user?
* *Answer*: 2024-05-29 06:36:40
* Look for event ID 4768 with the Ticket options as 0x40800010 from [here](https://www.blumira.com/blog/how-to-detect-as-rep-roasting) and using [python-evtx](https://github.com/williballenthin/python-evtx) in the command line with ```python3 ~/resources/python-evtx/scripts/evtx_dump_json.py campfire-2/Security.evtx | jq '.[] | select (.EventID=="4768")'```
2. Please confirm the User Account that was targeted by the attacker.
* *Answer*: arthur.kyle
* Look for event ID 4768 with the Ticket options as 0x40800010 from [here](https://www.blumira.com/blog/how-to-detect-as-rep-roasting) and using [python-evtx](https://github.com/williballenthin/python-evtx) in the command line with ```python3 ~/resources/python-evtx/scripts/evtx_dump_json.py campfire-2/Security.evtx | jq '.[] | select (.EventID=="4768")'```
3. What was the SID of the account?
* *Answer*: 
* Look for event ID 4768 with the Ticket options as 0x40800010 from [here](https://www.blumira.com/blog/how-to-detect-as-rep-roasting) and using [python-evtx](https://github.com/williballenthin/python-evtx) in the command line with ```python3 ~/resources/python-evtx/scripts/evtx_dump_json.py campfire-2/Security.evtx | jq '.[] | select (.EventID=="4768")'```
4. It is crucial to identify the compromised user account and the workstation responsible for this attack. Please list the internal IP address of the compromised asset to assist our threat-hunting team.
* *Answer*: 172.17.79.129
* Look for event ID 4768 with the Ticket options as 0x40800010 from [here](https://www.blumira.com/blog/how-to-detect-as-rep-roasting) and using [python-evtx](https://github.com/williballenthin/python-evtx) in the command line with ```python3 ~/resources/python-evtx/scripts/evtx_dump_json.py campfire-2/Security.evtx | jq '.[] | select (.EventID=="4768")'```
5. We do not have any artifacts from the source machine yet. Using the same DC Security logs, can you confirm the user account used to perform the ASREP Roasting attack so we can contain the compromised account/s?
* *Answer*: happy.grunwald
* Look for the IP address from the attemped ASEP Roasting and search within the logs for different usernames with that same machine using [python-evtx](https://github.com/williballenthin/python-evtx) in the command line with ```$ python3 ~/resources/python-evtx/scripts/evtx_dump_json.py campfire-2/Security.evtx | jq '.[] | select (.IpAddress=="::ffff:172.17.79.129")'
```