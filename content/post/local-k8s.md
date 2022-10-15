---
# Common-Defined params
title: "local k8s cluster inside virtualbox"
date: "2022-10-14"
description: "guide for setting up k8s for local development environment"
categories: []
tags:
  - "k3s"
  - "ansible"
  - "packer"
  - "vagrant"
  - "virtualbox"
# menu: main # Optional, add page to a menu. Options: main, side, footer

---

## Download and install VirtualBox
VirtualBox is the most stable open source virtualization software for local environment.

You can go to official VirtualBox [site](https://www.virtualbox.org/wiki/Downloads) to download and install.

## Install Vagrant
Vagrant is a virtual server automation CLI tool developed by HashCorp. It simplifies managing of VirtualBox instances.

For Mac users, simply execute the following. Official Vagrant site is [here](https://www.vagrantup.com).
```
brew install vagrant
```

Adding custom top level domain makes testing easiser. Install vagrant DNS plugin.
```bash
vagrant plugin install vagrant-dns
```

## Download k3s vagrant box
k3s is an implementation of k8s. It is well suited for produciton environment as well as IoT edge devices. It's developed by Rancher. You can visit their [site](https://rancher.com) for more information.

vagrant box is a compressed file for vagrant. It contains a VirtualBox server image file and vagrant meta data files. Our vagrant box is from official debian project.

Let's make a directory in `~/vagrant/k3s` and instruct vagrant to download our vagrant box.
```bash
mkdir -p ~/vagrant/k3s

cd ~/vagrant/k3s

vagrant box add debian/bullseye64 --provider virtualbox
```

## Configure vagrant instance
Let's add a `Vagrantfile`. You can customize the binding ip address or CPU & memory allocation with this file.

```bash
$ cat <<EOF>>Vagrantfile
Vagrant.configure("2") do |config|
  config.vm.box = "debian/bullseye64"

  config.vm.network "private_network", ip: "192.168.56.100"
  config.vm.network "private_network", ip: "192.168.56.101"

  config.vm.provider "virtualbox" do |vb|
    vb.name   = "k3s"
    vb.memory = "4096"
  end

  config.vm.hostname          = "k3s"

  config.dns.tld      = "devel"
  config.dns.patterns = [/^(\w+\.)(\w+).devel$/, /^(\w+).devel$/]

  config.ssh.insert_key       = false
  config.ssh.private_key_path = ['~/.vagrant.d/insecure_private_key', '~/.ssh/id_rsa']
  config.vm.provision "file", source: "~/.ssh/id_rsa.pub", destination: "~/.ssh/authorized_keys"

  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y curl
    curl -sfL https://get.k3s.io | sh -
  SHELL
end
EOF
```

Start the vagrant instance
```bash
$ vagrant up
```

## Retrieve kubeconfig file from the new cluster 
```bash
$ ssh -o UserKnownHostsFile=/dev/null \
      -o StrictHostKeyChecking=no \
      vagrant@192.168.56.100 "sudo cat /etc/rancher/k3s/k3s.yaml" \
      | sed 's/127\.0\.0\.1/192\.168\.56\.100/' \
      > ~/.kube/local.config
```

## Use kubectl
```bash
$ export KUBECONFIG=~/.kube/local.config

$ kubectl get nodes
```

some material referenced from [here](https://github.com/techiescamp/vagrant-kubeadm-kubernetes)