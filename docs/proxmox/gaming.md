---
layout: default
title: Gaming
parent: Proxmox
nav_order: 3
---

# Windows

___

## GPU passthrough

TODO romparser

## EAC bypass
qm.conf
```
args: -cpu 'hv-vendor-id=0123756792CD' -smbios 'type=0...' -smbios 'type=1...'
```

<details><summary>get_smbios.sh</summary>
  <pre>
    #!/bin/bash
    
    # See https://www.qemu.org/docs/master/system/invocation.html?highlight=smbios#hxtool-4
    declare -A smb0
    declare -A smb1
    declare -A smb2
    declare -A smb3
    declare -A smb4
    declare -A smb11
    declare -A smb17
    
    function addDmi () {
        declare -n smb="smb$1"
        local dmiFle="/sys/class/dmi/id/$3"
        if [[ -f "$dmiFle" ]]; then
            smb[$2]=$(cat "$dmiFle")
            return
        fi
    
        local dmiDec=$(dmidecode --string "$3")
        if [[ $? -eq 0 ]]; then
            smb[$2]="$dmiDec"
        else
            smb[$2]="Default string"
        fi
    }
    
    function addDmiField () {
        declare -n smb="smb$1"
        local dmiDec=$(dmidecode -t $1 | grep -E "\s$3:" | head -n1 | grep -E -o ':\s+.*$' | cut -c3-)
        smb[$2]="$dmiDec"
    }
    
    function addStr () {
        declare -n smb="smb$1"
        smb[$2]="$3"
    }
    
    function printSmbType () {
        declare -n smb="smb$1"
    
        echo -n "-smbios 'type=$1"
        for key in "${!smb[@]}"; do
            local val="${smb[$key]/,/,,}"
            if [[ -z "$val" ]]; then val="''"; fi
            echo -n ",$key=$val"
        done
        echo -n "' "
    }
    
    
    addDmi 0 vendor bios_vendor
    addDmi 0 version bios_version
    addDmi 0 date bios_date
    addDmi 0 release bios_release
    addStr 0 uefi on
    
    addDmi 1 manufacturer sys_vendor
    addDmi 1 product product_name
    addDmi 1 version product_version
    addDmi 1 serial product_serial
    addDmi 1 uuid product_uuid
    addDmi 1 sku product_sku
    addDmi 1 family product_family
    
    addDmi 2 manufacturer board_vendor
    addDmi 2 product board_name
    addDmi 2 version board_version
    addDmi 2 serial board_serial
    addDmi 2 asset board_asset_tag
    addDmiField 2 location 'Location In Chassis'
    
    addDmi 3 manufacturer chassis_vendor
    addDmi 3 version chassis_version
    addDmi 3 serial chassis_serial
    addDmi 3 asset chassis_asset_tag
    addDmiField 3 sku 'SKU Number'
    
    addDmiField 4 sock_pfx 'Socket Designation'
    addDmi 4 manufacturer processor-manufacturer
    addDmi 4 version processor-version
    addDmiField 4 serial 'Serial Number'
    addDmiField 4 asset 'Asset Tag'
    addDmi 4 part processor-family
    
    addStr 11 value 'Default string'
    
    addStr 17 loc_pfx 'DIMM 0'
    addStr 17 bank 'Bank 0'
    addDmiField 17 manufacturer 'Manufacturer'
    addDmiField 17 serial 'Serial Number'
    addDmiField 17 asset 'Asset Tag'
    addDmiField 17 part 'Part Number'
    addStr 17 speed 3200
    
    printSmbType 0
    printSmbType 1
    printSmbType 2
    printSmbType 3
    printSmbType 4
    printSmbType 11
    printSmbType 17
    
    echo ''
  </pre>
</details>
