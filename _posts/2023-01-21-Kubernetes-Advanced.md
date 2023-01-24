---
layout: post
title: "[Kubernetes] Kubernetes Advanced"
date: 2023-01-21
excerpt: "Kubernetes"
tags: [Kubernetes, Server, Docker]
comments: false
---

<center>Please read first <a href="https://leehuhlee.github.io/Kubernetes-Basic/">Kubernetes Basic</a>.</center>

# Code Review
  - Download <a href="https://github.com/sysnet4admin/_Lecture_k8s_learning.kit">Kubernetes Learning Kit</a>
  - Unzip on your `HashiCorp` folder.
  - Use `~\HashiCorp\_Lecture_k8s_learning.kit-main\ch1\1.5\k8s-min-5GiB-wo-add-nodes` and `~\HashiCorp\_Lecture_k8s_learning.kit-main\ch1\1.6` to see all settings files.

## Vagrantfile

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/0.jpg"><img src="/assets/img/posts/kubernetes_advanced/0.jpg"></a>
  <figcaption>Code Review</figcaption>
</figure>

{% highlight vi %}
# -*- mode: ruby -*-
# vi: set ft=ruby :
{% endhighlight %}
- Vagrantfile is made by Ruby.

{% highlight vi %}
## configuration variables ##
# max number of worker nodes
N = 3 
{% endhighlight %}
- Worker Nodes are three.

{% highlight vi %}
# each of components to install 
k8s_V = '1.20.2'           # Kubernetes 
docker_V = '19.03.14-3.el7' # Docker  
ctrd_V = '1.3.9-3.1.el7'   # Containerd 
## /configuration variables ##
{% endhighlight %}
- Versions for Kubernetes, Docker and ContainerD

### Master Node

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/1.jpg"><img src="/assets/img/posts/kubernetes_advanced/1.jpg"></a>
  <figcaption>Code Review</figcaption>
</figure>

{% highlight vi %}
Vagrant.configure("2") do |config|
{% endhighlight %}
- Current Vagrantfile Api Version is 2.
- `do-end` statement will run line 19~35.

{% highlight vi %}
config.vm.define "WO-m-k8s-#{k8s_V[0..3]}" do |cfg|
{% endhighlight %}
- `k8s_V` is a variable for kubernetes version and `0..3` means the index to read.
- For example, `k8s_V` is 1.20.0 and `k8s_V[0..3]` will get `1.20`.

{% highlight vi %}
cfg.vm.box = "sysnet4admin/CentOS-k8s"
{% endhighlight %}
- VM image is CentOS private image.

{% highlight vi %}
cfg.vm.provider "virtualbox" do |vb|
  vb.name = "WO-m-k8s-#{k8s_V[0..3]}(github_SysNet4Admin)"
  vb.cpus = 2
  vb.memory = 1746
  vb.customize ["modifyvm", :id, "--groups", "/WO-k8s-SgMST-#{k8s_V[0..3]}(github_SysNet4Admin)"]
end
{% endhighlight %}
- Our virtual machine software is virtual box.
- Master Node has 2 cpus.
- Master Node has 1746 MB.
- We made a group to set on/off easy.
- If you want to see your customized groups, enter `kubectl get cm kubelet-config-1.22 -n kube-system -o yaml | grep cgroup`.

{% highlight vi %}
cfg.vm.host_name = "m-k8s"
cfg.vm.network "private_network", ip: "192.168.1.10"
cfg.vm.network "forwarded_port", guest: 22, host: 60010, auto_correct: true, id: "ssh"
cfg.vm.synced_folder "../data", "/vagrant", disabled: true 
{% endhighlight %}
- Host name is `m-k8s`.
- `private_network` makes connection network with your pc.
- `forwarded_port` set local host to `60010`.
- When there is something wrong in our port, it will fix automatically. 
- Port ID is `ssh`.
- `synced_folder` will sync your pc folders with virtual machine folders, if `disabled` is false.

{% highlight vi %}
cfg.vm.provision "shell", path: "k8s_env_build.sh", args: N
cfg.vm.provision "shell", path: "k8s_pkg_cfg.sh", args: [ k8s_V, docker_V, ctrd_V ]
#cfg.vm.provision "shell", path: "master_node.sh"
cfg.vm.provision "shell", path: "k_cfg_n_git_clone.sh" # add kubectl config & git clone source
{% endhighlight %}
- Run `xxx.sh` file in shell with arguments.
- We seperated cluster part for practice.

### Worker Node

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/2.jpg"><img src="/assets/img/posts/kubernetes_advanced/2.jpg"></a>
  <figcaption>Code Review</figcaption>
</figure>

{% highlight vi %}
(1..N).each do |i|
{% endhighlight %}
- Loop statement 1 to 3.

{% highlight vi %}
config.vm.define "WO-w#{i}-k8s-#{k8s_V[0..3]}" do |cfg|
  cfg.vm.box = "sysnet4admin/CentOS-k8s"
  cfg.vm.provider "virtualbox" do |vb|
    vb.name = "WO-w#{i}-k8s-#{k8s_V[0..3]}(github_SysNet4Admin)"
    vb.cpus = 1
    vb.memory = 1024
    vb.customize ["modifyvm", :id, "--groups", "/WO-k8s-SgMST-#{k8s_V[0..3]}(github_SysNet4Admin)"]
  end
  cfg.vm.host_name = "w#{i}-k8s"
  cfg.vm.network "private_network", ip: "192.168.1.10#{i}"
  cfg.vm.network "forwarded_port", guest: 22, host: "6010#{i}", auto_correct: true, id: "ssh"
  cfg.vm.synced_folder "../data", "/vagrant", disabled: true
  cfg.vm.provision "shell", path: "k8s_env_build.sh", args: N
  cfg.vm.provision "shell", path: "k8s_pkg_cfg.sh", args: [ k8s_V, docker_V, ctrd_V ]
  #cfg.vm.provision "shell", path: "work_nodes.sh"
{% endhighlight %}
- Worker Node has 1 cpu.

## k8s_env_build.sh 

- This file will set kubernetes environment.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/3.jpg"><img src="/assets/img/posts/kubernetes_advanced/3.jpg"></a>
  <figcaption>Code Review</figcaption>
</figure>

{% highlight vi %}
#!/usr/bin/env bash
{% endhighlight %}
- This script will use bash shell script.

{% highlight vi %}
# vim configuration 
echo 'alias vi=vim' >> /etc/profile
{% endhighlight %}
- `vi` command is same with `vim`

{% highlight vi %}
# swapoff -a to disable swapping
swapoff -a
{% endhighlight %}
- Swap should be off to install kubernetes.

{% highlight vi %}
# sed to comment the swap partition in /etc/fstab
sed -i.bak -r 's/(.+ swap .+)/#\1/' /etc/fstab
{% endhighlight %}
- Swap is always off when you reboot the machine.

{% highlight vi %}
# kubernetes repo
gg_pkg="packages.cloud.google.com/yum/doc" # Due to shorten addr for key
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=https://${gg_pkg}/yum-key.gpg https://${gg_pkg}/rpm-package-key.gpg
EOF
{% endhighlight %}
- `gpgcheck` is off.
- `repo_gpgcheck` is off.
- If you need security, you can set those to 1.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/4.jpg"><img src="/assets/img/posts/kubernetes_advanced/4.jpg"></a>
  <figcaption>Code Review</figcaption>
</figure>

{% highlight vi %}
# add docker-ce repo
yum install yum-utils -y 
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
{% endhighlight %}
- Use `yum` to download docker community repository.

{% highlight vi %}
# Set SELinux in permissive mode (effectively disabling it)
setenforce 0
sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
{% endhighlight %}
- SELinux if off.
- If you need security, you can set this to on.

{% highlight vi %}
# RHEL/CentOS 7 have reported traffic issues being routed incorrectly due to iptables bypassed
cat <<EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
modprobe br_netfilter
{% endhighlight %}
- `br_netfilter` is bridge netfileter.
- `br_netfilter` will connect your machines to one network.

{% highlight vi %}
# local small dns & vagrant cannot parse and delivery shell code.
echo "192.168.1.10 m-k8s" >> /etc/hosts
for (( i=1; i<=$1; i++  )); do echo "192.168.1.10$i w$i-k8s" >> /etc/hosts; done
{% endhighlight %}
- Deploy node name automatically, for example, m-k8s or w1-k8s.
- `$1` is `k8s_v`.

{% highlight vi %}
# config DNS  
cat <<EOF > /etc/resolv.conf
nameserver 1.1.1.1 #cloudflare DNS
nameserver 8.8.8.8 #Google DNS
EOF
{% endhighlight %}
- DNS settings.

## k8s_env_build.sh 

- This script is for installing kubernetes.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/5.jpg"><img src="/assets/img/posts/kubernetes_advanced/5.jpg"></a>
  <figcaption>Code Review</figcaption>
</figure>

{% highlight vi %}
# install util packages 
yum install epel-release -y
yum install vim-enhanced -y
yum install git -y
{% endhighlight %}
- `epel-release` makes extended package for CentOs from Red Hat, examples, extended storage.
- `vim-enhanced` will install vim.
- You don't need to intall `git` but for practice.

{% highlight vi %}
# install docker 
yum install docker-ce-$2 docker-ce-cli-$2 containerd.io-$3 -y
{% endhighlight %}
- `$2` is `docker_V` and `$3` is `ctrd_V`.

{% highlight vi %}
# install kubernetes
# both kubelet and kubectl will install by dependency
# but aim to latest version. so fixed version by manually
yum install kubelet-$1 kubectl-$1 kubeadm-$1 -y 
{% endhighlight %}
- `$1` is `k8s_V`.

{% highlight vi %}
# Ready to install for k8s 
systemctl enable --now docker
systemctl enable --now kubelet
{% endhighlight %}
- Ready to system.

## k_cfg_n_git_clone.sh 

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/6.jpg"><img src="/assets/img/posts/kubernetes_advanced/6.jpg"></a>
  <figcaption>Code Review</figcaption>
</figure>

{% highlight vi %}
# install bash-completion for kubectl 
yum install bash-completion -y 

# kubectl completion on bash-completion dir
kubectl completion bash >/etc/bash_completion.d/kubectl
{% endhighlight %}
- `bash-completion` allow us to use auto kubectl commands.

{% highlight vi %}
# alias kubectl to k 
echo 'alias k=kubectl' >> ~/.bashrc
echo 'complete -F __start_kubectl k' >> ~/.bashrc
{% endhighlight %}
- `alias` makes short cut of commands.
- `complete -F __start_kubectl k` allow `k` to use `bash-completion`

{% highlight vi %}
# git clone k8s-code
git clone https://github.com/sysnet4admin/_Lecture_k8s_learning.kit.git
mv /home/vagrant/_Lecture_k8s_learning.kit $HOME
find $HOME/_Lecture_k8s_learning.kit -regex ".*\.\(sh\)" -exec chmod 700 {} \;
{% endhighlight %}
- Download practice codes from git.

## WO_master_node.sh

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/8.jpg"><img src="/assets/img/posts/kubernetes_advanced/8.jpg"></a>
  <figcaption>Code Review</figcaption>
</figure>

{% highlight vi %}
# init kubernetes 
kubeadm init --token 123456.1234567890123456 --token-ttl 0 \
--pod-network-cidr=172.16.0.0/16 --apiserver-advertise-address=192.168.1.10
{% endhighlight %}
- We need `token` to join Master Node and Worker Node.
- `token-ttl` expires the token in 24 hours.
- `pod-network-cidr` assigns pod's network.
- `apiserver-advertise-address` is fixed with Master Node IP address to avoid join problems.

{% highlight vi %}
# config for master node only 
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config
{% endhighlight %}
- To skip verification when we use kubectl.

{% highlight vi %}
# raw_address for gitcontent
raw_git="raw.githubusercontent.com/sysnet4admin/IaC/master/manifests" 

# config for kubernetes's network 
kubectl apply -f https://$raw_git/172.16_net_calico_v1.yaml
{% endhighlight %}
- Apply Calico for kubernetes network.

## WO_work_nodes.sh

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/9.jpg"><img src="/assets/img/posts/kubernetes_advanced/9.jpg"></a>
  <figcaption>Code Review</figcaption>
</figure>

{% highlight vi %}
# config for work_nodes only 
kubeadm join --token 123456.1234567890123456 \
             --discovery-token-unsafe-skip-ca-verification 192.168.1.10:6443
{% endhighlight %}
- Join with Master Node.

# IDE

## Deploy Kubernetes VM
- Open your `~HashiCorp\_Lecture_k8s_learning.kit-main\ch1\1.5\k8s-min-5GiB-wo-add-nodes` folder in command and use `vagrant up`.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/7.jpg"><img src="/assets/img/posts/kubernetes_advanced/7.jpg"></a>
  <figcaption>IDE</figcaption>
</figure>

- Open your SuperPutty and click [File]-[Import Sessions]-[From File] to import `~HashiCorp\_Lecture_k8s_learning.kit-main\ch1\1.5\Sessions(k8s_learning).XML`.

# Install Kubernetes with kubeadm

- Use `~/_Lecture_k8s_learning.kit/ch1/1.6/WO_master_node.sh` to install kubernetes in master node.
- Open to worker node #1, #2 and #3 and use script of `~/_Lecture_k8s_learning.kit/ch1/1.6/WO_worker_node.sh`.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/10.jpg"><img src="/assets/img/posts/kubernetes_advanced/10.jpg"></a>
  <figcaption>IDE</figcaption>
</figure>

- Open your `~HashiCorp\_Lecture_k8s_learning.kit-main\ch1\1.5\k8s-min-5GiB-wo-add-nodes>` folder and use `vagrant destroy -f` to delete all VM.
- We need to delete all VM to upgrade our VM.

# IDE 2
- Update Kubernetes, Docker and ContainerD.
- Upgrade memory in master and worker nodes.

## Deploy Kubernetes VM 
- Open your `~HashiCorp\_Lecture_k8s_learning.kit-main\ch2\2.1\k8s-UpTo-10GiB` folder in command and use `vagrant up`.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/11.jpg"><img src="/assets/img/posts/kubernetes_advanced/11.jpg"></a>
  <figcaption>IDE</figcaption>
</figure>

# Definitions

## Object

* Container
- Container has one software or system.

* Pod
- Pod has one or union of containers.
- Pod has a volume to save eternal data.

{% highlight yaml %}
apiVersion: v1 // pod version
kind: Pod // Object type
metadata: // information of pod
  labels:
    run: po-nginx
  name: po-nginx
spec: // spec of pod
  containers:
  - image: nginx
    name: nginx
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/21.jpg"><img src="/assets/img/posts/kubernetes_advanced/21.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

* Deployment

{% highlight yaml %}
apiVersion: apps/v1 // deployment version
kind: Deployment // object type
metadata: // information of deployment
  labels:
    app: deploy-nginx
  name: deploy-nginx
spec:  // spec of deployment
  replicas: 3  // replica set
  selector:  // choose templete
    matchLabels:
      app: po-nginx
  template:  // templete to make pod
    metadata:
      labels:
        app: po-nginx
    spec:
      containers:
      - name: nginx
        image: nginx
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/22.jpg"><img src="/assets/img/posts/kubernetes_advanced/22.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

* ReplicaSet
- Deployment needs ReplicaSet to manage count of pods

* Job
- You can use job to decrese using memory.

* CronJob
- Use Job with schedule.

* DaemonSet
- DaemonSet makes one pod on each nodes.

* StatefulSet
- StatefulSet saves state of pod.

* Application
- Pod(s) containing container(s) and volume for specific work is(are) an application.
- For example, NGINX, MySQL, etc.
- Even when you add something on the application, that is also an application.

## Commands

* get
- Read object

* run, create, apply
- Create object

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/12.jpg"><img src="/assets/img/posts/kubernetes_advanced/12.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

* delete
- Delet object

* exec
- Access to container in pod.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/14.jpg"><img src="/assets/img/posts/kubernetes_advanced/14.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

* scale
- Add or sub count of pods.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/13.jpg"><img src="/assets/img/posts/kubernetes_advanced/13.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

* edit
- Change deployed object.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/15.jpg"><img src="/assets/img/posts/kubernetes_advanced/15.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

* events
- Check events with namespace.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/18.jpg"><img src="/assets/img/posts/kubernetes_advanced/18.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

* describe
- Check status of object.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/19.jpg"><img src="/assets/img/posts/kubernetes_advanced/19.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

* logs
- Check log.
- Log is worten when deploy is successed.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/20.jpg"><img src="/assets/img/posts/kubernetes_advanced/20.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

### yaml

* -o yaml
- Read yaml code.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/16.jpg"><img src="/assets/img/posts/kubernetes_advanced/16.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

* --dry-run=client
- Run yaml code to read.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/17.jpg"><img src="/assets/img/posts/kubernetes_advanced/17.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

# Deploy Application




