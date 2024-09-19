
# Quick Links:
### Tools
[Autoruns - Sysinternals | Microsoft Learn](https://learn.microsoft.com/en-us/sysinternals/downloads/autoruns "https://learn.microsoft.com/en-us/sysinternals/downloads/autoruns")

### Commands
[Start-Process](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/start-process?view=powershell-7.4)
[Stop-Process](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/stop-process?view=powershell-7.4 "https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/stop-process?view=powershell-7.4")
[Start-Service](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/start-service?view=powershell-7.4 "https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/start-service?view=powershell-7.4")
[Stop-Service](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/stop-service?view=powershell-7.4)
[Set-ExecutionPolicy (Microsoft.PowerShell.Security) - PowerShell | Microsoft Learn](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7.4#-executionpolicy)
[Set-ExecutionPolicy (Microsoft.PowerShell.Security) - PowerShell | Microsoft Learn|Scopes](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7.4#-scope)
GPupdate - Updates local and domain group policy.

### Lessons
[Unlocking Process Monitor: The IT Admin's Hidden Gem for Troubleshooting - YouTube](https://www.youtube.com/watch?v=UL1msF9-bnk)
[Event Viewer & Windows Logs](https://www.youtube.com/watch?v=X0WDzktpr5I)

## Summary of what we worked on
Today we worked on looking over how to use tools to review system logs/windows logs as well as troubleshooting steps to work on the process of knowing how to figure out why an app may or may not work.

We reviewed the topical use of PowerShell and some of the commands.  These are mentioned in [[#Commands]] as well. We worked on the following `Start-Process` `Stop-Process` `Start-Service` `Stop-Service` `Set-ExecutionPolicy` `Get-Process (or gc)` Variables for example `$variable-name = "hello"`.

We also worked on reviewing other Admin based information and Windows Admin skills. This related to log viewing but also basic windows navigation. We discussed the 3 main type of Windows versions (Home, Pro, and Enterprise).
![[{84604B5D-5DB5-42CC-8374-7BCE6F471FCA}.png]]

There are also 2 mobile versions (Mobile, and Mobile Enterprise).

### How to quick open certain settings:
CPL

| execute      | Function                                                                                                           |
| ------------ | ------------------------------------------------------------------------------------------------------------------ |
| appwiz.cpl   | Opens the Programs and Features window where you can uninstall programs.                                           |
| desk.cpl     | Opens the Display settings.                                                                                        |
| inetcpl.cp   | Opens Internet Properties, where you can change internet options.                                                  |
| main.cpl     | Opens the Mouse properties.                                                                                        |
| mmsys.cpl    | Opens the Sound settings.                                                                                          |
| ncpa.cpl     | Opens Network Connections where you can view and change network settings.                                          |
| powercfg.cpl | Opens Power Options to adjust power plans.                                                                         |
| sysdm.cpl    | Opens System Properties, which shows basic information about your computer and provides access to system settings. |
| timedate.cpl | Opens the Date and Time settings.                                                                                  |
 MSC

|compmgmt.msc|Opens the Computer Management console|
|---|---|
|devmgmt.msc|Opens the Device Manager|
|diskmgmt.msc|Opens Disk Management,|
|eventvwr.msc|Opens the Event Viewer|
|gpedit.msc|Opens the Local Group Policy Editor|
|lusrmgr.msc|Opens the Local Users and Groups manager|
|services.msc|Opens the Services console|
|taskschd.msc|Opens the Task Scheduler|

### Group Policy (Local Group Policy Editor)
Basically a way to configure thousands of user and computer settings.

### Useful Troubleshooting stuff
[Logging for powershell about_Logging_Windows - PowerShell | Microsoft Learn]([https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_logging_windows?view=powershell-7.4)

command will get operating system details and installation and last boot times  
  Get-CimInstance -ClassName win32_operatingsystem -ComputerName grl-dc03,grl-sql01,grl-sql03 | select PSComputerName,caption,version,installdate,LastBootUpTime | Format-Table -AutoSize

example of using a hash table argument, also known as a _dictionary_, to retrieve account lockout events in the last twelve hours from a remote domain controller  
``` Get-WinEvent -ComputerName grl-dc03 -FilterHashtable @{LogName = 'Security';Id = 4740;StartTime = (Get-Date).AddHours( -12) } -ErrorAction SilentlyContinue | Select TimeCreated,@{n='User';e={$_.Properties[0].value}},@{n='From';e={$_.Properties[1].Value}}’ ```

 allows checking of a specific port on a machine  
``` Test-NetConnection -ComputerName grl-dc03 -Port 443 -InformationLevel Quiet ```

shows details in the given folder and children, including any hidden ones which the _-force_ enables, for any file that exceeds 100MB in size, sorted in descending size order.  
``` Get-ChildItem -Force -Recurse -File | Where Length -gt 100MB | Sort Length -Descending | Select fullname, CreationTime, @{n='Size (MB)'; e={[math]::Round($_.Length / 1MB , 2)}}}```

powershell info  
`$PSVersionTable`

### Event Viewer
logs you will need to review will depend on the specific situation. However, usually, you'll be in the "Windows Logs" reviewing the **Application**, **Security**, and **System** logs.

The "Error" log events are related to issues that you need to resolve as soon as possible. The "Warning" events are not critical, but those are events that you want to investigate. Finally, the "Information" logs are those events that are only informational.

You can create custom view of logs this might be useful when looking for specific logs

### Microsoft Server
#### MMC into command prompt
![[{D9F8124F-96C7-48FC-9497-CDC2DDCA7DD8}.png]]
Go to file -> add/remove snapins to add certain thing.
Great utility is Windows Admin Center that people can download which allows for manage computers through web interface.





Fun fact:
`powershell script %0 | %0|` = PS fork bomb
