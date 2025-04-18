---
layout: default
title: Build
parent: Vyos
nav_order: 1
---

# Build

___

## 1.3.3 LTS
### RTL88x2BU driver

```Dockerfile
FROM debian:latest
WORKDIR /build
RUN echo "deb [trusted=yes] http://dev.packages.vyos.net/repositories/equuleus equuleus main" > /etc/apt/sources.list.d/vyos.list
RUN apt update
RUN apt install dkms gcc git debhelper bc linux-headers-5.4.243-amd64-vyos -y
ADD ./build.sh /build.sh
CMD ["/bin/bash", "/build.sh"]

# in case debug needed, keep container running
#ENTRYPOINT ["tail", "-f", "/dev/null"]

```

```bash
#!/bin/bash
git clone https://github.com/cilynx/rtl88x2bu
cd rtl88x2bu
dkms build . -k 5.4.243-amd64-vyos
dkms mktarball --binaries-only rtl88x2bu/5.8.7.1 -k 5.4.243-amd64-vyos
cd /var/lib/dkms/rtl88x2bu/5.8.7.1/tarball/
tar -xvf rtl88x2bu-5.8.7.1-kernel5.4.243-amd64-vyos-x86_64.dkms.tar.gz
```

```bash
docker build -t vyos-kernel-env .
```

```bash
docker run --name vyos-rtl88x2bu vyos-kernel-env
```

```bash
docker cp "vyos-rtl88x2bu:/var/lib/dkms/rtl88x2bu/5.8.7.1/tarball/dkms_main_tree/5.4.243-amd64-vyos/x86_64/module/88x2bu.ko" .
```

```bash
mv 88x2bu.ko /lib/modules/5.4.243-amd64-vyos/kernel/drivers/88x2bu/
```

```bash
depmod
```

```bash
echo "88x2bu" >> /etc/modules
```