# PowerShell in Azure Cloud Shell for Windows users

In May 2018 we [announced](https://azure.microsoft.com/blog/pscloudshellrefresh/) changes coming to PowerShell in Azure Cloud Shell.  The PowerShell experience in Azure Cloud Shell is now PowerShell Core 6 in Linux.
With this change, there are some aspects of PowerShell in Cloud Shell that may be different than what is expected in Windows PowerShell 5.1.

## Case sensitivity

In Windows, the file system is case-insensitive.  In Linux, the file system is case sensitive.  This means that previously `file.txt` and `FILE.txt` were considered to be the same file, now they are considered to be different files.  Proper casing must be used while `tab` completing in the file system.  PowerShell specific experiences, such as `tab` cmdlets, are not case sensitive. 

## Windows PowerShell alias vs Linux utilities

Existing commands in Linux, such as `ls`, `sort`, and `sleep` take presidence over their PowerShell aliases.  Below are the removed aliases as well as the equivalent commands:  

|Removed Alias   |Equivalent Command   |
|---|---|
|`ls`     | `dir` <br> `Get-ChildItem` |
|`sort`   | `Sort-Object` |
|`sleep`  | `Start-Sleep` |

## Persisting $home vs $home\clouddrive

For users of who persisted their settings for tools such as Git and SSH in their Cloud Drive, the $HOME directory is now persisted across sessions.

## What's new in PowerShell Core 6

For more information about what is new in PowerShell Core 6, please reference the [PowerShell docs](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6)
