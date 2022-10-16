---
# Common-Defined params
title: "Vagrant box created using packer and ansible"
date: "2022-10-13"
description: "How to use vbox script to automate vagrant box build"
categories: []
tags:
  - "ansible"
  - "packer"
  - "vagrant"
# menu: main # Optional, add page to a menu. Options: main, side, footer
image: "company-packer-employee-desk-with-laptop.jpeg"
draft: true
---

## Goal
Vagrant cloud provides ready made vagrant boxes. We can customize our own box and upload it to vagrant cloud. Using our own vagrant box can speed things up.

### Script to download
```bash
curl -SO https://raw.githubusercontent.com/curiosinauts/playbooks/master/bin/vbox --output-dir ~/bin
```