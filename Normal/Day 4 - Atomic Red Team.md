******************************************************************************************************************************
                    Day 4 : I'm all atomic inside!
******************************************************************************************************************************

Learn how to identify malicious techniques using the MITRE ATT&CK framework.
Learn about how to use Atomic Red Team tests to conduct attack simulations.
Understand how to create alerting and detection rules from the attack tests.

Detection gaps are usually for one of two main reasons:

Security is a cat-and-mouse game. As we detect more, the threat actors and red teamers will find new sneaky ways to thwart our detection. We then need to study these novel techniques and update our signature and alert rules to detect these new techniques.
The line between anomalous and expected behaviour is often very fine and sometimes even has significant overlap. For example, let's say we are a company based in the US. We expect to see almost all of our logins come from IP addresses in the US. One day, we get a login event from an IP in the EU, which would be an anomaly. However, it could also be our CEO travelling for business. This is an example where normal and malicious behaviour intertwine, making it hard to create accurate detection rules that would not have too much noise.

Cyber Kill Chain

Initial Recon > Initial Compromise > Establish Foothold ( > Escalate Privileges > Internal Recon > Move Laterally > Mainain Presence ) > Complete Mission

MITRE ATT&CK
https://attack.mitre.org/
https://mitre-attack.github.io/attack-navigator/

Atomic Red Team library is a collection of red team test cases that are mapped to the MITRE ATT&CK framework

| Parameter          | Explanation                                                                                     | Example use                                                                                       |
|---------------------|-------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|
| `-AtomicTechnique` | This defines what technique you want to emulate. You can use the complete technique name or the "TXXXX" value. This flag can be omitted. | `Invoke-AtomicTest -AtomicTechnique T1566.001`                                                  |
| `-ShowDetails`     | Shows the details of each test included in the Atomic.                                          | `Invoke-AtomicTest T1566.001 -ShowDetails`                                                      |
| `-ShowDetailsBrief`| Shows the title of each test included in the Atomic.                                            | `Invoke-AtomicTest T1566.001 -ShowDetailsBrief`                                                 |
| `-CheckPrereqs`    | Provides a check if all necessary components are present for testing                            | `Invoke-AtomicTest T1566.001 -CheckPrereqs`                                                     |
| `-TestNames`       | Sets the tests you want to execute using the complete Atomic Test Name.                         | `Invoke-AtomicTest T1566.001 -TestNames "Download Macro-Enabled Phishing Attachment"`            |
| `-TestGuids`       | Sets the tests you want to execute using the unique test identifier.                            | `Invoke-AtomicTest T1566.001 -TestGuids 114ccff9-ae6d-4547-9ead-4cd69f687306`                   |
| `-TestNumbers`     | Sets the tests you want to execute using the test number. The scope is limited to the Atomic Technique. | `Invoke-AtomicTest T1566.001 -TestNumbers 2,3`                                                  |
| `-Cleanup`         | Run the cleanup commands that were configured to revert your machine state to normal.           | `Invoke-AtomicTest T1566.001 -TestNumbers 2 -Cleanup`                                           |

| Key                  | Value                                                                                           | Description                                                                                                                                                                   |
|-----------------------|-------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Technique            | Phishing: Spearphishing Attachment T1566.001                                                   | The full name of the MITRE ATT&CK technique that will be tested                                                                                                               |
| Atomic Test Name     | Download Macro-Enabled Phishing Attachment                                                     | A descriptive name of the type of test that will be executed                                                                                                                  |
| Atomic Test Number   | 1                                                                                              | A number is assigned to the test; we can use this in the command to specify which test we want to run.                                                                         |
| Atomic Test GUID     | 114ccff9-ae6d-4547-9ead-4cd69f687306                                                           | A unique ID is assigned to this test; we can use this in the command to specify which test we want to run.                                                                     |
| Description          | This atomic test downloads a macro-enabled document from the Atomic Red Team GitHub repository, simulating an end-user clicking a phishing link to download the file. The file "PhishingAttachment.xlsm" is downloaded to the %temp% directory. | Provides a detailed explanation of what the test will do.                                                                                                                     |
| Attack commands      | Executor: powershell<br>ElevationRequired: False<br>Command: `$url = 'http://localhost/PhishingAttachment.xlsm'; Invoke-WebRequest -Uri $url -OutFile $env:TEMP.xlsm` | This provides an overview of all the commands run during the test, including the executor of those commands and the required privileges. It also helps determine artefact locations in Windows Event Viewer. |
| Cleanup commands     | Command: `Remove-Item $env:TEMP.xlsm -ErrorAction Ignore`                                      | An overview of the commands executed to revert the machine back to its original state.                                                                                        |
| Dependencies         | There are no dependencies required.                                                            | An overview of all required resources that must be present on the testing machine in order to execute the test.                                                               |

There are several detection rule formats, including Yara, Sigma, Snort, and more

"powershell.exe" & {$url = 'http://localhost/PhishingAttachment.xlsm' Invoke-WebRequest -Uri $url -OutFile $env:TEMP\PhishingAttachment.xlsm}"

We can use multiple parts of this artefact to include in our custom Sigma rule.

Invoke-WebRequest: It is not common for this command to run from a script behind the scenes.

$url = 'http://localhost/PhishingAttachment.xlsm': Attackers often use a specific malicious domain to host their payloads. Including the malicious URL in the Sigma rule could help us detect that specific URL.

PhishingAttachment.xlsm: This is the malicious payload downloaded and saved on our system. We can include its name in the Sigma rule as well.

PowerShell Invoke-WebRequest Sigma Rule

title: Detect PowerShell Invoke-WebRequest and File Creation of PhishingAttachment.xlsm
  id: 1
  description: Detects the usage of Invoke-WebRequest to download PhishingAttachment.xlsm and the creation of the file PhishingAttachment.xlsm.
 status: experimental
  author: TryHackMe
  logsource:
    category: process_creation
    product: windows
    service: sysmon
  detection:
   selection_invoke_webrequest:
      EventID: 1
      CommandLine|contains:
        - 'Invoke-WebRequest'
        - 'http://localhost/PhishingAttachment.xlsm'
    
    selection_file_creation:
      EventID: 11  # Sysmon Event ID for File Creation
      TargetFilename|endswith: '\PhishingAttachment.xlsm'
      
    condition: selection_invoke_webrequest or selection_file_creation
  falsepositives:
    - Legitimate administration activity may use Invoke-WebRequest, and legitimate Excel files may be created with similar names.
  level: high
  tags:
    - attack.t1071.001   # Web Service - Application Layer Protocol
    - attack.t1059.001   # PowerShell
    - attack.t1105       # Ingress Tool Transfer
    - attack.t1566.001   # Spearphishing Attachment

******************************************
                Investigation
******************************************        


https://attack.mitre.org/techniques/T1059/

https://attack.mitre.org/techniques/T1059/003/

*****************************************************
                Commandline Arguments
*****************************************************

Get-Help Invoke-Atomictest
Invoke-AtomicTest T1566.001
Invoke-AtomicTest T1566.001 -TestNumbers 1 -CheckPrereq
Invoke-AtomicTest T1566.001 -TestNumbers 1 -cleanup

"powershell.exe" & {$url = 'http://localhost/PhishingAttachment.xlsm' Invoke-WebRequest -Uri $url -OutFile $env:TEMP\PhishingAttachment.xlsm}"




*****************************************************
                Section 1
*****************************************************

*****************************************************
                Section 2
*****************************************************

*****************************************************
                Section 3
*****************************************************