---
layout: default
title: Pangolin
parent: Linux
nav_order: 4
---

# Pangolin installation on AlmaLinux with rootless podman

## Firewalld

```
dnf install -y firewalld podman epel-release && dnf install -y podman-compose
systemctl enable --now firewalld
firewall-cmd --add-service=http --permanent
firewall-cmd --add-service=https --permanent
firewall-cmd --add-port=51820/udp --permanent
firewall-cmd --add-port=21820/udp --permanent
firewall-cmd --reload
echo 'net.ipv4.ip_unprivileged_port_start=80' >> /etc/sysctl.conf && sysctl -p
```

**DO NOT START CONTAINERS YET**
```
adduser pangolin
su - pangolin
loginctl enable-linger 1001
curl -fsSL https://pangolin.net/get-installer.sh | bash
```

**DO NOT START CONTAINERS YET**
```
./installer
```

Add selinux flag to config volumes
```
:z
```

Add ee- before image tag for enterprise edition
```
ee-version
```

Start pangolin
```
podman compose up -d
```
___
*Reference: https://docs.pangolin.net/self-host/quick-install*