---
layout: default
title: Windows facts
parent: Ansible
nav_order: 2
---
# Windows facts

___

A powershell script can be used to store values in a json format :
```
$someValue = value

@{
    othervalue = $someValue
}
```


```
- hosts: windows
  tasks:
    - name: Set Windows Ansible facts folder, and add informations to setup vars
      setup:
        fact_path: "C:\\Program Files\\Ansible\\facts"
      register: setupvar
    - debug:
        var: ansible_windows_fact.othervalue
```

{: .important }
The variable to call depends of the file name uploaded on the Windows server, prefixed by *ansible_*
If the script is called *windows_fact.ps1*, ther variable to call will be *ansible_windows_fact.othervalue*