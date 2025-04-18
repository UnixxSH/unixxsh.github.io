---
layout: default
title: Certificates
parent: Windows
nav_order: 2
---

# Certificates

___

## convert crt/key to pks on Windows
```
openssl pkcs12 –export –out certificate.pfx –inkey rsaprivate.key –in certificate.crt –certfile fileca.crt
```

## convert pem to crt
```
openssl x509 -outform der -in your-cert.pem -out your-cert.crt
```
