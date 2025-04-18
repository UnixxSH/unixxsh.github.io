---
layout: default
title: dockerd.conf
parent: Alarms
grand_parent: Netdata
nav_order: 1
---

# dockerd.conf
```
 template: docker_containers_state
       on: docker.containers_state
    class: Errors
     type: Containers
component: Docker
    units: running containers
    every: 10s
   lookup: min -10s of running
     crit: $this < 2
     info: containers down, to check ASAP
       to: sysadmin
```