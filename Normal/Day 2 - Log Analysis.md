******************************************************************************************************************************
                    Day 2 - One man's false positive is another man's potpourri
******************************************************************************************************************************

- Identify if the alert is a True Positive or False Positive
- An alert is considered a TP if it contains actual malicious activity
- When they are unsure whether an activity is performed by a malicious actor or a legitimate user, they can just confirm with the user. This privilege is not available to the attacker.

There are cases which can act as Kryptonite to the SOC superpower:
- If an organisation doesn't have a change request process in place.
- The performed activity was outside the scope of the change request or was different from that of the approved change request.
- The activity triggered an alert, such as copying files to a certain location, uploading a file to some website, or a failed login to a system. 
- An insider threat performed an activity they are not authorised to perform, whether intentionally or unintentionally.
- A user performed a malicious activity via social engineering from a threat actor.

Context + Correlation

- Correlation entails using the past and future events to recreate a timeline of events
- Important artefacts can include IP addresses, machine names, user names, hashes, file paths, etc.

******************************************
                Investigation
******************************************        

Alerts recieved on 1st Dec between 09:00 and 09:30

- ADM-01 - 10.0.11.11
- WareHost-01 - 10.0.5.1
- WareHost-02 - 10.0.5.2
- WareHost-03 - 10.0.5.3
- WareHost-04 - 10.0.5.4
- WareHost-05 - 10.0.5.5
- WareHost-06 - 10.0.5.6
- WareHost-07 - 10.0.5.7
- WareHost-08 - 10.0.5.8
- WareHost-09 - 10.0.5.9
- WareHost-10 - 10.0.5.10
- Glitch      - 10.0.255.1

Login - Dec 1, 2024 @ 09:03:31.000

Logins

service_admin

*****************************************************
                Commandline Arguments
*****************************************************

"C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" -Command "$credential = Get-Credential; $credential | Export-Clixml -Path 'C:\service_admin-creds.xml'"

"C:\Windows\System32\notepad.exe" "C:\remote-wares-updates.ps1"

"C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" -EncodedCommand SQBuAHMAdABhAGwAbAAtAFcAaQBuAGQAbwB3AHMAVQBwAGQAYQB0AGUAIAAtAEEAYwBjAGUAcAB0AEEAbABsACAALQBBAHUAdABvAFIAZQBiAG8AbwB0AA==

Install-WindowsUpdate -AcceptAll -AutoReboot

c:\Windows\system32\vssadmin.exe  delete shadows /all /quiet

