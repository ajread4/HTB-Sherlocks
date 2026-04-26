# Meerkat

Hack the Box Link: [Meerkat](https://app.hackthebox.com/sherlocks/Meerkat)

1. We believe our Business Management Platform server has been compromised. Please can you confirm the name of the application running?
* *Answer*: BonitaSoft
* Looking at the wireshark there was a lot of "bonita" wtihin the logs. Therefore, I googled the name and found the full name of the Business Management Software 
2. We believe the attacker may have used a subset of the brute forcing attack category - what is the name of the attack carried out?
* *Answer*: Credential Stuffing
* Look through the HTTP data within the wireshark pcap to find a bunch of username and password tests
3. Does the vulnerability exploited have a CVE assigned - and if so, which one?
* *Answer*: CVE-2022-25237
* Look through the alerts.json using jq to find the Bonitasoft vuln that was identified based on the attacker IP using ```$ cat meerkat-alerts.json | jq '.[] | select (.src_ip=="156.146.62.213")'```
4. Which string was appended to the API URL path to bypass the authorization filter by the attacker's exploit?
* *Answer*: i18ntranslation
* Looking at the specific CVE, that is the string that must be added to the API URL path to bypass 
5. How many combinations of usernames and passwords were used in the credential stuffing attack?
* *Answer*: 56
* Use tshark to find the number with ```$ tshark -Y "http.request.method==POST and ip.src ==156.146.62.213 and http.request.uri contains loginservice" -r meerkat.pcap -T fields -e text | cut -d " " -f 7,11 | sort | uniq | wc -l```
6. Which username and password combination was successful?
* *Answer*: seb.broom@forela.co.uk:g0vernm3nt
* Look through the HTTP POST requests to the login service for the only one with a 200 status. 
7. If any, which text sharing site did the attacker utilise?
* *Answer*: pastes.io
* Look through the HTTP data within wireshark to find the url with a successful GET request. 
8. Please provide the filename of the public key used by the attacker to gain persistence on our host.
* *Answer*: hffgra4unv
* Navigate to the pastes.io link that comes up in the wirehark capture 
9. Can you confirmed the file modified by the attacker to gain persistence?
* *Answer*: home/ubuntu/.ssh/authorized_keys
* Navigate to the pastes.io link to find the location where they are placing authorized keys 
10. Can you confirm the MITRE technique ID of this type of persistence mechanism?
* *Answer*: T1098.004
* Google SSH authorized keys MITRE ATT&CK technique 