# Compromised

Hack the Box Link: [Compromised](https://app.hackthebox.com/sherlocks/Compromised)

1. What is the IP address used for initial access?
* *Answer*: 162.252.172.54
2. What is the SHA256 hash of the malware?
* *Answer*: 9b8ffdc8ba2b2caa485cca56a82b2dcbd251f65fb30bc88f0ac3da6704e4d3c6
* Download the malware using wireshark and run ```sha256sum``` on the command line 
3. What is the Family label of the malware?
* *Answer*: pikabot
* Look up the hash in virus total 
4. When was the malware first seen in the wild (UTC)?
* *Answer*: 2023-05-19 14:01:21
5. The malware used HTTPS traffic with a self-signed certificate. What are the ports, from smallest to largest?
* *Answer*: 2078, 2222, 32999
* To check for self-signed, look in wireshark for the Certificate, Server Key Exchange, Server Hello Done packet (or Client) and see if the issuer and the subject are the same. 
6. What is the id-at-localityName of the self-signed certificate associated with the first malicious IP?
* *Answer*: Pyopneumopericardium
* Look at the Server Hello response packet from the first malicious IP of 45.85.235.39 which occurs directly after the execution of the malware.
7. What is the notBefore time(UTC) for this self-signed certificate?
* *Answer*: 2023-05-14 08:36:52 (UTC)
* Look at the Server Hello response packet from the first malicious IP of 45.85.235.39 which occurs directly after the execution of the malware 
8. What was the domain used for tunneling?
* *Answer*: steasteel.net
* All of the DNS traffic in the packet capture 