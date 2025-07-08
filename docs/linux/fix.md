---
layout: default
title: Fixs
parent: Linux
nav_order: 2
---

# Fixs

___

## video stutter (Fedora)
```bash
dnf groupupdate multimedia --setop="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin

dnf groupupdate sound-and-video 
```

___

## AppImage icon fix at runtime (alacarte added)

Get application WMClass
```bash
xprop | grep WM_CLASS
```

Add this line into application .desktop (.local/share/applications/)
```
...
StartupWMClass=<class>
```