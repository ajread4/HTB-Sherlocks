# Bumblebee

Hack the Box Link: [Bumblebee](https://app.hackthebox.com/sherlocks/Bumblebee)

1. What was the username of the external contractor?
* *Answer*: apoole1 
* Looked through the sql logs in the command line with ```sqlite3```, dumped the tables with ```.tables``` and did a ```select * from phpbb_users```. 
2. What IP address did the contractor use to create their account?
* *Answer*: 10.10.0.78
* Look through the access.log file for the many GET and POST requests to the server 
3. What is the post_id of the malicious post that the contractor made?
* *Answer*: 9
* Had to look up the table schema with ```.schema phpbb_posts``` and then review the posts in the command line with ```select * from phpbb_posts;```. 
What is the full URI that the credential stealer sends its data to?
* *Answer*: http[:]//10[.]10[.]0[.]78/update[.]php
* Look through the HTML of the post in sql with ```select * from phpbb_posts where post_id==9;```. 
When did the contractor log into the forum as the administrator? (UTC)
* *Answer*: 26/04/2023 10:53:12
* Look at the phpbb_log table to find the epoch time within ```61|0|48|0|0|0|0|10.10.0.78|1682506392|LOG_ADMIN_AUTH_SUCCESS|```. 
In the forum there are plaintext credentials for the LDAP connection, what is the password?
* *Answer*: Passw0rd1
* Look through the phpbb_config file to find the ldap password. 
What is the user agent of the Administrator user?
* *Answer*: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.0.0 Safari/537.36
* Look through the access.log and find the various user agents. The admin one is from 10.255.254.2, not the 10.10.0.78. 
What time did the contractor add themselves to the Administrator group? (UTC)
* *Answer*: 26/04/2023 10:53:51
* Look at the phpbb_log and find the line with ```62|0|48|0|0|0|0|10.10.0.78|1682506431|LOG_USERS_ADDED|a:2:{i:0;s:14:"Administrators";i:1;s:6:"apoole";}```
What time did the contractor download the database backup? (UTC)
* *Answer*: 26/04/2023 11:01:38
* Look through the access.log for the GET request with 200 status code and a download of something related to backup: ```10.10.0.78 - - [26/Apr/2023:12:01:38 +0100] "GET /store/backup_1682506471_dcsr71p7fyijoyq8.sql.gz HTTP/1.1" 200 34707 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/112.0"```
What was the size in bytes of the database backup as stated by access.log?
* *Answer*: 34707
* Look through the access.log for the GET request with 200 status code and a download of something related to backup: ```10.10.0.78 - - [26/Apr/2023:12:01:38 +0100] "GET /store/backup_1682506471_dcsr71p7fyijoyq8.sql.gz HTTP/1.1" 200 34707 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/112.0"```