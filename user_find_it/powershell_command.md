user ip address and login time via powershell
=============================================

## user ip address and last login time
```
Get-WinEvent -ComputerName ctl.com -LogName Security |
Where-Object {
    $_.Id -eq 4624 -and $_.Properties[5].Value -eq "mlechard"
} |
Select-Object @{
    Name = "Client IP"; Expression = { $_.Properties[18].Value }
}, @{
    Name = "Workstation Name"; Expression = { $_.Properties[11].Value }
}, TimeCreated |
Sort-Object TimeCreated -Descending |
Select-Object -First 1
```
