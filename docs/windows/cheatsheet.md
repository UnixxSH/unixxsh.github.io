---
layout: default
title: Cheatsheet
parent: Windows
nav_order: 1
---

# Cheatsheet

___

## Merge Linux splitted files (split)

```
copy /b x* <file_name>
```

___

## Set network conf
```powershell
Set-NetIPInterface -InterfaceAlias Ethernet -Dhcp Disabled
Clear-DnsClientCache
Get-NetAdapter -Name Ethernet| New-NetIPAddress -IPAddress [[ip]] -DefaultGateway [[gtw]] -PrefixLength [[/prefix]]
Get-NetAdapter -Name Ethernet |Set-DNSClientServerAddress -ServerAddresses [[ip]]
Set-NetAdapterAdvancedProperty -Name Ethernet -DisplayName "VLAN ID" -DisplayValue [[vlan]]
```

```powershell
Set-NetIPInterface -InterfaceAlias Ethernet -Dhcp Enabled
Set-DnsClientServerAddress -InterfaceAlias Ethernet -ResetServerAddresses
Restart-NetAdapter -InterfaceAlias Ethernet
Set-NetIPInterface -InterfaceAlias Ethernet| Remove-NetRoute -Confirm:$false
Remove-NetRoute -InterfaceAlias "Ethernet" -NextHop [[gtw]] -Confirm:$false
Clear-DnsClientCache
```

___

## Set shortcut arguments
```powershell
$Shell = New-Object -ComObject ("WScript.Shell")
$ShortCut = $Shell.CreateShortcut("[[shortcut]].lnk")
$ShortCut.TargetPath="C:\Program Files\Mozilla Firefox\firefox.exe"
$ShortCut.Arguments="""-private""" + " " + """[[link]]"""
$ShortCut.WorkingDirectory = "C:\Program Files\Mozilla Firefox\"
$ShortCut.Description = "[[desc]]"
$ShortCut.Save()
```

___

## Get folders size in current location (not sorted)
```powershell
Get-ChildItem | Where-Object { $_.PSIsContainer } | ForEach-Object { $_.Name + ": " + "{0:N2}" -f ((Get-ChildItem $_ -Recurse | Measure-Object Length -Sum -ErrorAction SilentlyContinue).Sum / 1MB) + " MB" }
```

___

## Get the users that modified a specific folder in the timeframe
```powershell
Get-ChildItem C:\Users\*\.cache | Where{$_.LastWriteTime -gt (Get-Date).AddDays(-61)}| Select Parent
```