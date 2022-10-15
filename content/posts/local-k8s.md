---
# Common-Defined params
title: "local k8s cluster inside virtualbox"
date: "2022-10-14"
description: "Example article description"
categories:
  - "cloud"
  - "automation"
tags:
  - "k3s"
  - "ansible"
  - "packer"
  - "vagrant"
  - "virtualbox"
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

## Download and install VirtualBox
VirtualBox is the most stable open source virtualization software for local environment.

You can go to official VirtualBox [site](https://www.virtualbox.org/wiki/Downloads) to download and install.

## Install Vagrant
Vagrant is a virtual server automation CLI tool developed by HashCorp. It simplifies managing of VirtualBox instances.

For Mac users, simply execute the following. Official Vagrant site is [here](https://www.vagrantup.com).
```
$ brew install vagrant
```

Test vagrant install
```
$ vagrant --version
Vagrant 2.3.0
```

## Download k3s vagrant box
k3s is an implementation of k8s. It is well suited for produciton environment as well as IoT edge devices. It's developed by Rancher. You can visit their [site](https://rancher.com) for more information.

vagrant box is a compressed file for vagrant. It contains a VirtualBox server image file and vagrant meta data files. Our vagrant box is from official debian project.

Let's make a directory in `~/vagrant/k3s` and instruct vagrant to download our vagrant box.
```
$ mkdir -p ~/vagrant/k3s

$ cd ~/vagrant/k3s

$ vagrant box add debian/bullseye64 --provider virtualbox
```

## Configure vagrant instance
Let's add a `Vagrantfile`. You can customize the binding ip address or CPU & memory allocation with this file.

```
$ cat <<EOF>>Vagrantfile
Vagrant.configure("2") do |config|
  config.vm.box = "debian/bullseye64"

  config.vm.network "private_network", ip: "192.168.56.100"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = "4096"
  end

  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y curl
    curl -sfL https://get.k3s.io | sh -
  SHELL
end
EOF
```

Start the vagrant instance
```
$ vagrant up
```

## Retrieve KUBECONFIG private key
```
$ vagrant ssh

$ sudo cat /etc/rancher/k3s/k3s.yaml
```

## Use kubectl
