******************************************************************************************************************************
                    Day 6 - If I can't find a nice malware to use, I'm not going.
******************************************************************************************************************************

Analyze malware behaviour using sandbox tools.
Explore how to use YARA rules to detect malicious patterns.
Learn about various malware evasion techniques.
Implement an evasion technique to bypass YARA rule detection.

A sandbox is an isolated environment where (malicious) code is executed without affecting anything outside the system. Often, multiple tools are installed to monitor, record, and analyze the code's behaviour


HKLM\\Software\\Microsoft\\Windows\\CurrentVersion

C Function is designed to check the system's registry for a specified directory path (ProgramFilesDir)

void registryCheck() {
    const char *registryPath = "HKLM\\Software\\Microsoft\\Windows\\CurrentVersion";
    const char *valueName = "ProgramFilesDir";
    
    // Prepare the command string for reg.exe
    char command[512];
    snprintf(command, sizeof(command), "reg query \"%s\" /v %s", registryPath, valueName);
    // Run the command
    int result = system(command);
    // Check for successful execution
    if (result == 0) {
        printf("Registry query executed successfully.\n");
    } else {
        fprintf(stderr, "Failed to execute registry query.\n");
    }
}
int main() {
    const char *flag = "[REDACTED]";
    registryCheck();
        return 0;

} 

YARA is a tool used to identify and classify malware based on patterns in its code. By writing custom rules, analysts can define specific characteristics to look for—such as particular strings, file headers, or behaviours—and YARA will scan files or processes to find matches, making it invaluable for detecting malicious code.

rule SANDBOXDETECTED
{
    meta:
        description = "Detects the sandbox by querying the registry key for Program Path"
        author = "TryHackMe"
        date = "2024-10-08"
        version = "1.1"

    strings:
        
    $cmd= "Software\\Microsoft\\Windows\\CurrentVersion\" /v ProgramFilesDir" nocase

    

    condition:
        $cmd
}

In the strings section, we have defined variables that include the value to look out for: $cmd
In the condition section, we define when the rule will match the scanned file. In this case, if any of the specified strings are present. 

**More Evasion Techniques**

void registryCheck() {
// Encoded PowerShell command to query the registry
    const char *encodedCommand = "RwBlAHQALQBJAHQAZQBtAFAAcgBvAHAAZQByAHQAeQAgAC0AUABhAHQAaAAgACIASABLAEwATQA6AFwAUwBvAGYAdAB3AGEAcgBlAFwATQBpAGMAcgBvAHMAbwBmAHQAXABXAGkAbgBkAG8AdwBzAFwAQwB1AHIAcgBlAG4AdABWAGUAcgBzAGkAbwBuACIAIAAtAE4AYQBtAGUAIABQAHIAbwBnAHIAYQBtAEYAaQBsAGUAcwBEAGkAcgA=";
    // Prepare the PowerShell execution command
    char command[512];
    snprintf(command, sizeof(command), "powershell -EncodedCommand %s", encodedCommand);

    // Run the command
    int result = system(command);

    // Check for successful execution
    if (result == 0) {
        printf("Registry query executed successfully.\n");
    } else {
        fprintf(stderr, "Failed to execute registry query.\n");
    }  
}

Floss, a powerful tool developed by Mandiant that functions similarly to the Linux strings tool but is optimized for malware analysis

floss.exe C:\Tools\Malware\MerryChristmas.exe: This command scans for strings in the binary MerryChrismas.exe. If any hardcoded variables were defined in the malware, Floss should find them.
The | symbol redirects the output of the command in front of it to the input of the command behind it.
Out-file C:\tools\malstrings.txt: We save the command results in a file called malstrings.txt.

Sysmon, a tool from Microsoft's Sysinternals suite, continuously monitors and logs system activity across reboots. This Windows service provides detailed event data on process creation, network connections, and file changes—valuable insights when tracing malware behaviour.

<QueryList>
  <Query Id="0" Path="Microsoft-Windows-Sysmon/Operational">
    <Select Path="Microsoft-Windows-Sysmon/Operational">
      *[System[(EventRecordID="INSERT_EVENT_record_ID_HERE")]]
    </Select>
  </Query>
</QueryList>

EventData that is valuable to us:

The ParentImage key shows us which parent process spawned the cmd.exe process to execute the registry check. We can see it was our malware located at C:\Tools\Malware\MerryChristmas.exe.
The ParentProcessId and ProcessId keys are valuable for follow-up research. We could also use them to check other logs for related events.
The User key can help us determine which privileges were used to run the cmd.exe command. The malware could have created a hidden account and used that to run commands.
The CommandLine key shows which command was run in detail, helping us identify the malware's actions.
The UtcTime key is essential for creating a time frame for the malware's operation. This time frame can help you focus your threat hunting efforts.

******************************************
                Investigation
******************************************        











*****************************************************
                Commandline Arguments
*****************************************************











*****************************************************
                Section 1
*****************************************************

*****************************************************
                Section 2
*****************************************************

*****************************************************
                Section 3
*****************************************************