---
# Common-Defined params
title: "Local k8s cluster inside virtualbox"
date: "2022-10-14"
description: "guide for setting up k8s for local development environment"
categories: []
tags:
  - "k3s"
  - "vagrant"
  - "virtualbox"
# menu: main # Optional, add page to a menu. Options: main, side, footer
image: "25501.jpg"
---

## Installation
We want a production like k8s cluster running on local dev machine that is isolated by virtual server. We will leverage VirtualBox, vagrant, and k3s to achieve this.

### VirtualBox
VirtualBox is the most stable open source virtualization software for local environment.
You can go to official VirtualBox [site](https://www.virtualbox.org/wiki/Downloads) to download and install.

___

### Vagrant
Vagrant is a virtual server automation CLI tool developed by HashCorp. It simplifies managing of VirtualBox instances. Official Vagrant site is [here](https://www.vagrantup.com).

For Mac users, simply execute the following. 
```
brew install vagrant
```

Adding custom top level domain makes testing easiser. Install vagrant DNS plugin.
```bash
vagrant plugin install vagrant-dns
```

___

### Vagrant Box
vagrant box is a compressed file for vagrant. It contains a VirtualBox server image file and vagrant meta data files. Our vagrant box is from official debian project.

Let's make a directory in `~/vagrant/k8s` and instruct vagrant to download our vagrant box.
```bash
mkdir -p ~/vagrant/k8s

cd ~/vagrant/k8s

vagrant box add curiosityworks/k3s --provider virtualbox
```

___

## Configuration
k3s is an implementation of k8s. It is well suited for produciton environment as well as IoT and edge devices. It's developed by Rancher. You can visit their [site](https://rancher.com) for more information.

Let's add a `Vagrantfile`. You can customize the binding ip address or CPU & memory allocation with this file. This will instruct vagrant to install k3s during the first start up phase. Please refer to line number 25.

### Vagrantfile
```bash
cat <<EOF>>Vagrantfile
Vagrant.configure("2") do |config|
  config.vm.box = "curiosityworks/k3s"

  (100..102).each do |i|
  config.vm.network "private_network", ip: "192.168.56." + "#{i}"
  end

  config.vm.provider "virtualbox" do |vb, override|
    vb.name   = "k3s"
    vb.memory = "4096"
    vb.cpus   = 6
    vb.customize [
      "modifyvm", :id,
      "--natdnshostresolver1", "on",
      # some systems also need this:
      # "--natdnshostresolver2", "on"
    ]
  end

  config.vm.hostname          = "k3s"

  config.dns.tld      = "devel"
  config.dns.patterns = [/^(\w+\.)(\w+).devel$/, /^(\w+).devel$/]

  config.ssh.insert_key       = false
  config.ssh.private_key_path = ['~/.vagrant.d/insecure_private_key', '~/.ssh/id_rsa']
  config.vm.provision "file", source: "~/.ssh/id_rsa.pub", destination: "~/.ssh/authorized_keys"
end
EOF

```

Start the vagrant instance
```bash
vagrant up
```

### Kubeconfig
Retrieve kubeconfig file from the new cluster by executing the following
```bash
ssh -o UserKnownHostsFile=/dev/null \
    -o StrictHostKeyChecking=no \
    vagrant@192.168.56.100 "sudo cat /etc/rancher/k3s/k3s.yaml" \
    | sed 's/127\.0\.0\.1/192\.168\.56\.100/' \
    | sed 's/default/local/g' > ~/.kube/local.config
```
___

## Test
Use kubectl
```bash
export KUBECONFIG=~/.kube/local.config

kubectl get nodes
```

## Post Install Metallb Setup 
```bash
helm repo add metallb https://metallb.github.io/metallb

kubectl create namespace metallb-system

helm install metallb metallb/metallb --wait --namespace metallb-system

cat <<EOF | kubectl apply -f -
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: internal-addresses
  namespace: metallb-system
spec:
  addresses:
  - 192.168.56.100-192.168.56.107
---
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: l2
  namespace: metallb-system
EOF

```

### Install test Nginx
```
helm repo add bitnami https://charts.bitnami.com/bitnami

helm install nginx bitnami/nginx --set service.type="ClusterIP" --wait
```

### Add ingress 
```
cat <<EOF | kubectl apply -f -
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app 
  namespace: default
spec:
  rules:
  - host: my.app.devel
    http:
      paths:
      - backend:
          service:
            name: nginx 
            port:
              name: http
        path: /
        pathType: Prefix
EOF
```

Header image designed by [starline / Freepik](https://www.freepik.com/free-vector/gorgeous-clouds-background-with-blue-sky-design_8562848.htm#query=cloud&position=30&from_view=keyword") 