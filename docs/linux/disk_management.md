---
layout: default
title: Disk management
parent: Linux
nav_order: 1
---
# Disk management

___

## Force disk disconnect
```bash
echo 1 | tee /sys/block/[[device]]/device/delete
```

___

## Force disk rescan
```bash
echo 1 | tee /sys/block/[[device]]/device/rescan
```

___

## Most disk usage
```bash
du -h [[dir]] 2>/dev/null | grep '[0-9\.]\+G'
```
