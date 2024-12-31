******************************************************************************************************************************
                    Day 15 - Be it ever so heinous, there's no place like Domain Controller
******************************************************************************************************************************

Learn about the structures of Active Directory.
Learn about common Active Directory attacks.
Investigate a breach against an Active Directory.

Common Active Directory Attacks
Adversaries are always looking for ways to breach and exploit Active Directory environments to destabilise and cause havoc to organisations. Working with Glitch to secure SOC-mas requires us to know common attacks and their mitigation measures.

Golden tickets representation.

Golden Ticket Attack

A Golden Ticket attack allows attackers to exploit the Kerberos protocol and impersonate any account on the AD by forging a Ticket Granting Ticket (TGT). By compromising the krbtgt account and using its password hash, the attackers gain complete control over the domain for as long as the forged ticket remains valid. The attack requires four critical pieces of information to be successful:

Fully Qualified Domain Name (FQDN) of the domain
SID of the domain
Username of an account to impersonate
KRBTGT account password hash
Detection for this type of attack involves monitoring for unusual activity involving the krbtgt

Event ID 4768: Look for TGT requests for high-privilege accounts.
Event ID 4672: This logs when special privileges (such as SeTcbPrivilege) are assigned to a user.
Pass-the-Hash

This type of attack steals the hash of a password and can be used to authenticate to services without needing the actual password. This is possible because the NTLM protocol allows authentication based on password hashes.

Key ways to mitigate this attack are enforcing strong password policies, conducting regular audits on account privileges, and implementing multi-factor authentication across the domain.

Kerberoasting

Kerberoasting is an attack targeting Kerberos in which the attacker requests service tickets for accounts with Service Principal Names (SPNs), extracts the tickets and password hashes, and then attempts to crack them offline to retrieve the plaintext password.

Mitigation for this type of attack involves ensuring that service accounts are secured with strong passwords, and therefore, implementing secure policies across the AD would be the defence.

Pass-the-Ticket

In a Pass-the-Ticket attack, attackers steal Kerberos tickets from a compromised machine and use them to authenticate as the user or service whose ticket was stolen.

This attack can be detected through monitoring for suspicious logins using Event ID 4768 (TGT request), especially if a user is logging in from unusual locations or devices. Additionally, Event ID 4624 (successful login) will reveal tickets being used for authentication.

Malicious GPOs

Adversaries are known to abuse Group Policy to create persistent, privileged access accounts and distribute and execute malware by setting up policies that mimic software deployment across entire domains. With escalated privileges across the domain, attackers can create GPOs to accomplish goals at scale, including disabling core security software and features such as firewalls, antivirus, security updates, and logging. Additionally, scheduled tasks can be created to execute malicious scripts or exfiltration data from affected devices across the domain.

To mitigate against the exploitation of Group Policy, GPOs need to be regularly audited for unauthorised changes. Strict permissions and procedures for GPO modifications should also be enforced.

Skeleton Key Attack

In a Skeleton Key attack, attackers install a malware backdoor to log into any account using a master password. The legitimate password for each account would remain unchanged, but attackers can bypass it using the skeleton key password.

| Event ID | Description                                                                               |
|----------|-------------------------------------------------------------------------------------------|
| 4624     | A user account has logged on                                                             |
| 4625     | A user account failed to log on                                                          |
| 4672     | Special privileges (i.e. SeTcbPrivilege) have been assigned to a user                   |
| 4768     | A TGT (Kerberos) ticket was requested for a high-privileged account                     |







******************************************
                Investigation
******************************************        











*****************************************************
                Commandline Arguments
*****************************************************

Get-GPO -All
Get-GPO -All | Where-Object { $_.ModificationTime } | Select-Object DisplayName, ModificationTime
Search-ADAccount -LockedOut | Select-Object Name, SamAccountName, LockedOut, LastLogonDate, DistinguishedName
%APPDATA%\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt








*****************************************************
                Section 1
*****************************************************

*****************************************************
                Section 2
*****************************************************

*****************************************************
                Section 3
*****************************************************