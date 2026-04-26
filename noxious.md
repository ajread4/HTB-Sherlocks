# Noxious

Hack the Box Link: [Noxious](https://app.hackthebox.com/sherlocks/Noxious)

1. Its suspected by the security team that there was a rogue device in Forela's internal network running responder tool to perform an LLMNR Poisoning attack. Please find the malicious IP Address of the machine.
* *Answer*: 172.17.79.135
* Filter for LLMNR traffic within wireshark, identify the machine with a bunch of requests to the DC. 
2. What is the hostname of the rogue machine?
* *Answer*: kali
* Look at the DHCP traffic from the suspected machine to the .254 within the LAN to find a section for host name. 
3. Now we need to confirm whether the attacker captured the user's hash and it is crackable!! What is the username whose hash was captured?
* *Answer*: john.deacon
* Look at the traffic to and from the malicious IP, there is RDP traffic in there with an msthash 
4. In NTLM traffic we can see that the victim credentials were relayed multiple times to the attacker's machine. When were the hashes captured the First time?
* *Answer*: 2024-06-24 11:18:30
* Look at the UTC arrival time of the packet in wireshark for the first challenge that occured where the packet was "Session Setup Response, Error: STATUS_MORE_PROCESSING_REQUIRED, NTLMSSP_CHALLENGE"
5. What was the typo made by the victim when navigating to the file share that caused his credentials to be leaked?
* *Answer*: DCC01
* That is not a real DC name, just need to go through LLMNR protocol logs and find oddities that occur with source of .136 machine to other parts of the VLAN. Can also see the typo within each of the NTLM_AUTH logs under the NTLMv2 response section
6. To get the actual credentials of the victim user we need to stitch together multiple values from the ntlm negotiation packets. What is the NTLM server challenge value?
* *Answer*: 601019d191f054f1
* Filter for ntlmssp within wireshark and find the server challenge within the Session Setup Response packet called "Session Setup Response, Error: STATUS_MORE_PROCESSING_REQUIRED, NTLMSSP_CHALLENGE"
7. Now doing something similar find the NTProofStr value.
* *Answer*: c0cc803a6d9fb5a9082253a04dbd4cd4
* Look ath the Session Setup Request, NTLMSSP_AUTH log and drill down within NTLMv2 Response for the NTProofStr. 
8. To test the password complexity, try recovering the password from the information found from packet capture. This is a crucial step as this way we can find whether the attacker was able to crack this and how quickly.
* *Answer*: NotMyPassword0k?
* Use [NTLMRawUnHide](https://github.com/mlgualtieri/NTLMRawUnHide) to pull the hash from wireshark, run it through hashcat with:```hashcat -a 0 -m 5600 ~/Downloads/noxious/hashes.txt SecLists-master/Passwords/Leaked-Databases/rockyou.txt``` The format for the hash is john.deacon::FORELA:[NTLM Server Challenge]:[NTProofStr]:[Rest of NTLMv2 Response after NTProofStr]
9. Just to get more context surrounding the incident, what is the actual file share that the victim was trying to navigate to?
* *Answer*: \\DC01\DC-Confidential
* Look within the wireshark packet capture for "smb2" in the filter. "Tree Connect Request (0x03)" contains the request for the share. 