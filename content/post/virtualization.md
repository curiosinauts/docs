---
# Common-Defined params
title: "apt cacher ng"
date: "2022-10-12"
description: "How to setup apt cacher ng"
categories: []
tags:
  - "apt"
# menu: main # Optional, add page to a menu. Options: main, side, footer
image: "Wavy_Tech-01_Single-10.jpg"
draft: true
---

## Host server set up

### set up apt-cacher-ng server
https://fabianlee.org/2018/02/11/ubuntu-a-centralized-apt-package-cache-using-apt-cacher-ng/

https://blog.packagecloud.io/eng/2015/05/05/using-apt-cacher-ng-with-ssl-tls/

### no password for current user
use visudo change the line sudo
```
%sudo  ALL=(ALL) NOPASSWD: ALL
```

### update the apt packages
```
sudo apt-get update
sudo apt-get upgrade
```

optional
```
sudo apt-get dist-upgrade 
```

http://apt-cache.curiosityworks.org:3142/acng-report.html?doCount=Count+Data#stats  


[Image by vectorjuice on Freepik](https://www.freepik.com/free-vector/web-page-visualization-protocol-procedure-dynamic-software-workflow-full-stack-development-markup-administrate-system-driver-shared-memory-vector-isolated-concept-metaphor-illustration_12470220.htm#query=cache%20memory&position=0&from_view=search&track=sph")
