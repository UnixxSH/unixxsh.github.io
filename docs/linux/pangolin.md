---
layout: default
title: Pangolin
parent: Linux
nav_order: 4
---

# Pangolin installation on AlmaLinux 10 with rootless quadlets

## Requirements

```
dnf install -y firewalld podman vim
systemctl enable --now firewalld
firewall-cmd --add-service=http --permanent
firewall-cmd --add-service=https --permanent
firewall-cmd --add-port=51820/udp --permanent
firewall-cmd --add-port=21820/udp --permanent
firewall-cmd --reload
echo 'net.ipv4.ip_unprivileged_port_start=80' >> /etc/sysctl.conf && sysctl -p
```

## User setup

```
useradd pangolin
usermod -aG systemd-journal pangolin
su - pangolin
loginctl enable-linger pangolin
```

## Environment setup

```
mkdir -p .config/containers/systemd
mkdir -p config/traefik/logs
```

Get all [files](https://github.com/UnixxSH/unixxsh.github.io/tree/main/docs/linux/pangolin_quadlets) and put them in ~/.config/containers/systemd

```
sed -i 's/PLACEHOLDER/CHANGEME_HOSTIP/g' .config/containers/systemd/pangolin.pod
systemctl --user daemon-reload
```

**DO NOT START CONTAINERS**
```
curl -fsSL https://pangolin.net/get-installer.sh | bash
./installer
```

Edit config/config.yml if needed

## Start application

```
systemctl --user enable --now app
systemctl --user enable --now gerbil
systemctl --user enable --now traefik
```

___
References: 
  - https://docs.pangolin.net/self-host/quick-install
  - https://github.com/orgs/fosrl/discussions/1609