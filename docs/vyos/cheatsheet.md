---
layout: default
title: Cheatsheet
parent: Vyos
nav_order: 2
---

# Cheatsheet

___

## vyos-1x templates path

```
/opt/vyatta/share/vyatta-cfg/templates
```

___

## openvpn client template

```
client
dev tun
proto udp
remote vpn.liawtdne.fr 1194
resolv-retry infinite
persist-key
persist-tun
remote-cert-tls server
cipher AES-256-GCM
nobind
<ca>
-----BEGIN CERTIFICATE-----
-----END CERTIFICATE-----
</ca>
<cert>
-----BEGIN CERTIFICATE-----
-----END CERTIFICATE-----
</cert>
<key>
-----BEGIN PRIVATE KEY-----
-----END PRIVATE KEY-----
</key>
```
