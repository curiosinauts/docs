---
# Common-Defined params
title: "apt cacher ng"
date: "2022-10-12"
description: "How to setup apt cacher ng"
categories:
  - "cloud"
  - "automation"
tags:
  - "apt"
# menu: main # Optional, add page to a menu. Options: main, side, footer

# Theme-Defined params
# thumbnail: "img/placeholder.png" # Thumbnail image
lead: "setup guide" # Lead text
comments: false # Enable Disqus comments for specific page
authorbox: false # Enable authorbox for specific page
pager: true # Enable pager navigation (prev/next) for specific page
toc: true # Enable Table of Contents for specific page
mathjax: true # Enable MathJax for specific page
sidebar: "right" # Enable sidebar (on the right side) per page
widgets: # Enable sidebar widgets in given order per page
  - "search"
  - "recent"
  - "taglist"
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
