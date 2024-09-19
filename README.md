# BroadTrainStuff
For Reasons

# Welcome to Train Stuff

In this document we like trains... 

What kind of trains? Idk, all type of trains honestly! You decide :)

Also, here is a PowerShell bible for stuff discussed earlier. - Jared 

Welcome to the PowerShell bible, I am making this in order to help with the troubleshooting and automating.

## Remote Management
#### Windows Remote
`winrm quickconfig`
- Quickly configure the windows remote.
#### New-PSSession
`New-PSSession -ComputerName <remote-server>`
- The New-PSSession cmdlet allows you to create a persistent connection to a remote computer.
#### Enter-PSSession
`enter-pssession -computername "<computer-name>"`
- The Enter-PSSession cmdlet allows you to enter an interactive session with a remote computer.
#### Get-WindowsFeatures
`get-windowsfeatures`
- List all windows features (Can check what is enabled and disabled)
#### Install-WindowsFeatures
`install-windowsfeature -Name <feature-name> -includeallsubfeature -includemanagementtools`
- Install/enable a windows feature. The last 2 flags just make sure to install sub features and install management tools.

### Executing Remote Commands
#### Invoke-Command
`Invoke-Command -ComputerName RemoteServer -ScriptBlock { Get-Service }`

## Starting and Stopping Services
#### Start-Service
`Start-Service -Name <Service-Name`
- This will start a cmdlet and is used to initiate a service that is currently stopped.
#### Stop-Service
`Stop-Service -Name <Service-Name>`
- The Stop-Service cmdlet is used to halt a currently running service.
#### Restart-Service
`Restart-Service -Name <Service-Name>`
- The Restart-Service cmdlet combines the functions of both starting and stopping a service.

## User and Group Management
### Creating and Managing Users
#### New-LocalUser
`New-LocalUser -Name "<name>" -Description "<user-description>"`
- The New-LocalUser cmdlet allows you to create a new local user account on a Windows system.
#### Set-Local User
`Set-LocalUser -Name "<name>" -Description "<user-description>"`
- The Set-LocalUser cmdlet is used to modify the properties of an existing local user account.
#### Remove-LocalUser
`Remove-LocalUser -Name "<name>"`
- The Remove-LocalUser cmdlet allows you to delete a local user account from the system.
#### New-AdUser
`new-aduser -name "<users-name>"`
- Adds a new Active Directory user

### Managing Groups
#### New-LocalGroup
`Add-LocalGroupMemeber -Group "<group-name>" -Member "<member-to-add-to-group>"`
- The New-LocalGroup cmdlet is used to create a new local group on a Windows system.
#### Remove-LocalGroupMember
`Remove-LocalGroupMemeber -Group "<group-name>" -Member "<member-to-remove-from-group>"`
- The Add-LocalGroupMember cmdlet is used to add a user or group to an existing local group.

## Networking
### Checking Network Configuration
#### Get-NetAdapter
`Get-NetAdapter`
- The Get-NetAdapter cmdlet allows you to retrieve information about network adapters on your system.
#### Test-NetConnection
`Test-NetConnection -ComputerName <comp-name(example.com)> -Port 80`
- The Test-NetConnection cmdlet helps you diagnose network connectivity issues.
#### Get-NetTCPConnection
`Get-NetTCPConnection`
- List all of the current active ports (PowerShell version of netstat).

### Configuring Network Settings
#### Set-NetIPAddress
`Set-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress "<IP-to-set-to>" -PrefixLength 24`
- The Set-NetIPAdress cmdlet allows you to configure the IP address settings of a network adapter.
#### Set-DnsClientServerAddress
`Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses ("8.8.8.8", "8.8.4.4")`
- The Set-DnsClientServerAddress cmdlet allows you to configure the DNS server addresses used by a network adapter.

## Monitoring System Resources
### Checking CPU Usage
#### Get-Counter
`Get-Counter '\Processor(_Total)\% Processor Time'`
- The Get-Counter cmdlet allows you to retrieve performance counter data, including CPU usage.
### Monitoring Memory Usage
#### Get-WmiObject
`Get-WmiObject -Class Win32_OperatingSystem | Select-Object TotalVisibleMemorySize,FreePhysicalMemory`
- The Get-WmiObject cmdlet retrieves management information by using Windows Management Instrumentation (WMI).
### Review Event Logs
#### Get-EventLog
`Get-EventLog -LogName Application -Newest 10`
- The Get-EventLog cmdlet allows you to retrieve events from event logs on the local or remote computers.

# Scripts
***
#### Find Scheduled tasks that are running
`(get-scheduledtask).where({$_.state -eq 'running'})`
Here’s what each part does:
- **`get-scheduledtask`**: This cmdlet retrieves all scheduled tasks on the local computer.
- **`.where({$_.state -eq 'running'})`**: This method filters the tasks, selecting only those whose `State` property is equal to `'running'`.

#### Find the Last Bootup Time
````ps1
Get-WmiObject win32_operatingsystem |select @{Name="Last Boot Time"; Expression={$_.ConvertToDateTime($_.LastBootUpTime)}}, PSComputerName
````
Here’s a breakdown of what each part does:
- **`Get-WmiObject win32_operatingsystem`**: This retrieves information about the operating system using the `win32_operatingsystem` WMI class.
- **`| select`**: This pipes the results to the `Select-Object` cmdlet (aliased as `select`), which allows you to select specific properties or computed properties.
- **`@{Name="Last Boot Time"; Expression={$_.ConvertToDateTime($_.LastBootUpTime)}}`**: This is a computed property that takes the `LastBootUpTime` property (which is in a format that’s not human-readable) and converts it to a standard date/time format using the `ConvertToDateTime` method. It then renames the property to “Last Boot Time.”
- **`PSComputerName`**: This selects the computer name property.

#### Find out how big a folder is
````ps1
dir -path C:\Scripts -file -recurse -force | measure-object length -sum -max -average | Select-Object @{name="Total Files";Expression={$_.count}},@{name="Largest File(MB)";Expression={"{0:F2}" -f ($_.maximum/1MB)}},@{name="Average Size(MB)";Expression={"{0:F2}" -f ($_.average/1MB)}},@{name="Total Size(MB)";Expression={"{0:F2}" -f ($_.sum/1MB)}}
````
Here’s a breakdown of each part of the command:
1. **`dir -path C:\Scripts -file -recurse -force`**: This uses the `dir` alias for the `Get-ChildItem` cmdlet to list all files (`-file`) in the `C:\Scripts` directory and its subdirectories (`-recurse`). The `-force` switch includes hidden and system files.
2. **`| measure-object length -sum -max -average`**: This pipes the results to the `Measure-Object` cmdlet, which calculates the sum, maximum, and average of the `length` property (file size) of the items.
3. **`| Select-Object`**: This pipes the results to `Select-Object`, which is used to create custom properties for the output object:
    - **`Total Files`**: The count of files.
    - **`Largest File(MB)`**: The size of the largest file, formatted to two decimal places and converted to megabytes.
    - **`Average Size(MB)`**: The average file size, formatted to two decimal places and converted to megabytes.
    - **`Total Size(MB)`**: The total size of all files, formatted to two decimal places and converted to megabytes.

#### Extract all unique IP addresses from a log file and display the top 10 most frequently occurring ones.
````ps1
Get-Content C:\MyLogFile.log | Select-String -Pattern "\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}" | Select-Object -ExpandProperty Matches | Select-Object -ExpandProperty Value | Sort-Object | Get-Unique | Group-Object | Sort-Object -Property Count -Descending | Select-Object -First 10
````
Here’s a step-by-step breakdown:
1. **`Get-Content C:\MyLogFile.log`**: Reads the content of the file located at `C:\MyLogFile.log`.
2. **`Select-String -Pattern "\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}"`**: Searches the content for any pattern matching an IP address, using a regular expression (`\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}`) that represents the four octets of an IP address.
3. **`Select-Object -ExpandProperty Matches`**: Expands the `Matches` property, which contains the matching values (IP addresses).
4. **`Select-Object -ExpandProperty Value`**: Extracts the actual value of each match, i.e., the IP address.
5. **`Sort-Object`**: Sorts the IP addresses. Note that this sort is lexicographic because it’s treating the IPs as strings, not as numerical values. This means that “10.0.0.2” will come before “2.0.0.2”.
6. **`Get-Unique`**: Removes duplicate IP addresses, leaving only unique ones.
7. **`Group-Object`**: Groups the unique IP addresses, counting how many times each one appears in the log file.
8. **`Sort-Object -Property Count -Descending`**: Sorts the grouped objects by the `Count` property in descending order, so the most frequent IP addresses come first.
9. **`Select-Object -First 10`**: Selects the first 10 objects, which represent the 10 most frequent IP addresses.

#### Monitor a website for availability and send an email notification whenever it goes down.
````ps1
$url = "https://www.example.com" 
$interval = 60 # seconds 
while ($true) { 
	try { 
		Invoke-WebRequest -Uri $url -UseBasicParsing -TimeoutSec 30 | Out-Null 
		Write-Host "$url is up." 
	} 
	catch { 
		$body = "$url is down. Error message: $_." 
		$params = @{ 
			To = "youremail@yourdomain.com" 
			From = "powershell@yourdomain.com" 
			Subject = "$url is down" 
			Body = $body SmtpServer = "smtp.yourdomain.com" 
		} 
		Send-MailMessage @params 
	} 
	Start-Sleep -Seconds $interval 
}
````
Here’s a step-by-step explanation:
1. **Variable Initialization**:
    - `$url`: The URL you want to monitor.
    - `$interval`: The number of seconds to wait between checks.
2. **Infinite Loop**: The `while ($true)` creates an infinite loop, which means this script will continue to run until it is manually stopped.
3. **Website Monitoring**:
    - **`Invoke-WebRequest`**: This command sends an HTTP request to the specified URL. The `-UseBasicParsing` switch simplifies the parsing, and the `-TimeoutSec 30` specifies a 30-second timeout for the request.
    - If the request is successful, `"$url is up."` is written to the host (console).
    - If the request fails (e.g., the website is down or the request times out), an email is sent with the error message.
4. **Email Notification**:
    - If an exception is caught (due to a failure in accessing the URL), an email is sent to the specified address with details of the error.
5. **Sleep**:
    - `Start-Sleep -Seconds $interval`: This command pauses the script for the specified interval (in this case, 60 seconds) before the next iteration of the loop.

***
