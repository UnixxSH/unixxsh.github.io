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

___

## Get old Chrome version
```powershell
$ChromeVersion = '115'

$requestId = ([String][Guid]::NewGuid()).ToUpper()
$sessionId = ([String][Guid]::NewGuid()).ToUpper()

$xml = @"
<?xml version="1.0" encoding="UTF-8"?>
<request protocol="3.0" updater="Omaha" sessionid="{$sessionId}"
    installsource="update3web-ondemand" requestid="{$requestId}">
    <os platform="win" version="10.0" arch="x64" />
    <app appid="{8A69D345-D564-463C-AFF1-A69D9E530F96}" version="5.0.375"
        ap="x64-stable-statsdef_0" lang="" brand="GCEB">
        <updatecheck targetversionprefix="$ChromeVersion"/>
    </app>
</request>
"@

$webRequest = @{
    Method    = 'Post'
    Uri       = 'https://tools.google.com/service/update2'
    Headers   = @{
        'Content-Type' = 'application/x-www-form-urlencoded'
        'X-Goog-Update-Interactivity' = 'fg'
    }
    Body      = $Xml
}

$result = Invoke-WebRequest @webRequest -UseBasicParsing
$contentXml = [xml]$result.Content
$status = $contentXml.response.app.updatecheck.status
if ($status -eq 'ok') {
    $package = $contentXml.response.app.updatecheck.manifest.packages.package
    $urls = $contentXml.response.app.updatecheck.urls.url | ForEach-Object { $_.codebase + $package.name }
    Write-Output "--- Chrome Windows 64-bit found. Hash=$($package.hash) Hash_sha256=$($package.hash_sha256)). ---"
    Write-Output $urls
}
else {
    Write-Output "Chrome not found (status: $status)"
}
```
source: https://stackoverflow.com/a/75555314
