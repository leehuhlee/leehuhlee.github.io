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
apiVersion: v1 # pod version
kind: Pod # Object type
metadata: # information of pod
  labels:
    run: po-nginx
  name: po-nginx
spec: # spec of pod
  containers:  # information of container
  - image: nginx
    name: nginx
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/23.jpg"><img src="/assets/img/posts/kubernetes_advanced/23.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/21.jpg"><img src="/assets/img/posts/kubernetes_advanced/21.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

* Deployment

{% highlight yaml %}
apiVersion: apps/v1 # deployment version
kind: Deployment # object type
metadata: # information of deployment
  labels:
    app: deploy-nginx
  name: deploy-nginx
spec:  # spec of deployment
  replicas: 3  # replica set
  selector:  # choose templete
    matchLabels:
      app: po-nginx
  template:  # templete to make pod
    metadata:
      labels:
        app: po-nginx
    spec:
      containers:  # information of container
      - name: nginx  
        image: nginx  # container image
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/24.jpg"><img src="/assets/img/posts/kubernetes_advanced/24.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/22.jpg"><img src="/assets/img/posts/kubernetes_advanced/22.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

* ReplicaSet
- Deployment needs ReplicaSet to manage count of pods

{% highlight yaml %}
apiVersion: apps/v1 # replicaset version
kind: ReplicaSet # object type
metadata: # information of replicaset
  labels:
    app: rs-nginx
  name: rs-nginx
spec:  # spec of replicaset
  replicas: 3
  selector:  # choose templete
    matchLabels:
      app: po-nginx
  template:  # templete to make pod
    metadata:
      labels:
        app: po-nginx
    spec:
      containers:  # information of container
      - image: nginx  # container image
        name: nginx
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/25.jpg"><img src="/assets/img/posts/kubernetes_advanced/25.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

- Honestly, the code is really similler with deployment, but we need ReplicaSet for rolling.
- For example, when you upgrade a pod, Deployment will create ReplicaSet, and ReplilcaSet will duplicate itself.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/26.jpg"><img src="/assets/img/posts/kubernetes_advanced/26.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/27.jpg"><img src="/assets/img/posts/kubernetes_advanced/27.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

* Job
- You can use job to decrese using memory.

{% highlight yaml %}
apiVersion: batch/v1  # job version
kind: Job  # object type
metadata:  # information of job
  name: job-curl-succ
spec:  # spec of job
  template:  # templete to make job
    spec:
      containers: # information of container
      - name: net-tools
        image: sysnet4admin/net-tools  # container image
        command: ["curlchk",  "nginx"]
      restartPolicy: Never  # restart option
{% endhighlight %}

- `restartPolicy` default value in other object is `Always` and it will restart the object forever.
- `restartPolicy` should be in job and this value should be `OnFailure` or `Never`.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/34.jpg"><img src="/assets/img/posts/kubernetes_advanced/34.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/33.jpg"><img src="/assets/img/posts/kubernetes_advanced/33.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

{% highlight yaml %}
apiVersion: batch/v1  # job version
kind: Job  # object type
metadata:  # information of job
  name: job-completions 
spec:  # spec of job
  completions: 3  # run sequentially 3 times
  template:  # templete to make job
    spec:
      containers:
      - name: net-tools
        image: sysnet4admin/net-tools  # container image
        command: ["curlchk",  "nginx"]
      restartPolicy: Never  # restart option
{% endhighlight %}

- Use `completions` to run to sequentially.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/35.jpg"><img src="/assets/img/posts/kubernetes_advanced/35.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

{% highlight yaml %}
apiVersion: batch/v1  # job version
kind: Job  # object type
metadata:  # information of job
  name: job-parallelism
spec:  # spec of job
  parallelism: 3  # run parallely 3 times
  template:  # templete to make job
    spec:
      containers:
      - name: net-tools
        image: sysnet4admin/net-tools  # container image
        command: ["curlchk",  "nginx"]
      restartPolicy: Never  # restart option
{% endhighlight %}

- Use `parallelism` to run to parallely.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/36.jpg"><img src="/assets/img/posts/kubernetes_advanced/36.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

{% highlight yaml %}
apiVersion: batch/v1  # job version
kind: Job  # object type
metadata:  # information of job
  name: job-activedeadlineseconds
spec:  # spec of job
  backoffLimit: 3
  activeDeadlineSeconds: 30  # dead time after run command
  template:  # templete to make job
    spec:
      containers:
      - name: net-tools
        image: sysnet4admin/net-tools  # container image
        command: ["/bin/sh", "-c"]
        args:
        - sleep 60;
          curlchk nginx;  
      restartPolicy: Never  # restart option
{% endhighlight %}

- Use `activeDeadlineSeconds` to delete on specific time after your command 

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/37.jpg"><img src="/assets/img/posts/kubernetes_advanced/37.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

{% highlight yaml %}
apiVersion: batch/v1  # job version
kind: Job  # object type
metadata: # information of job
  name: job-ttlsecondsafterfinished
spec:  # spec of job
  backoffLimit: 3
  ttlSecondsAfterFinished: 30 # dead time after completed
  template:  # templete to make job
    spec:
      containers:
      - name: net-tools
        image: sysnet4admin/net-tools  # container image
        command: ["/bin/sh", "-c"]
        args:
        - sleep 60;
          curlchk nginx;  
      restartPolicy: Never  # restart option
{% endhighlight %}

- Use `ttlSecondsAfterFinished` to delete on specific time after completed

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/38.jpg"><img src="/assets/img/posts/kubernetes_advanced/38.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

* CronJob
- Use `CronJob` to run Job with schedule.

{% highlight yaml %}
apiVersion: batch/v1  # cron job version
kind: CronJob  #  object type
metadata:  # information of cron job
  name: cj-1m-hist3-curl 
spec:  # spec of cron job
  schedule: "*/1 * * * *"  # cron rule
  jobTemplate:             # Template for job 
    spec:                  # same as before 
      template:  # templete to make job
        spec:
          containers:
          - name: net-tools
            image: sysnet4admin/net-tools  # container image
            command: ["curlchk",  "nginx"]
          restartPolicy: Never   # restart option
{% endhighlight %}

- cron rule : `*/#` repeats the job # periods, and just `#` repeats the job at #.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/39.jpg"><img src="/assets/img/posts/kubernetes_advanced/39.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

{% highlight yaml %}
apiVersion: batch/v1  # cron job version
kind: CronJob  #  object type
metadata:  # information of cron job
  name: cj-1m-hist10-curl 
spec:  # spec of cron job
  schedule: "*/1 * * * *"   # cron rule
  successfulJobsHistoryLimit: 10
  jobTemplate:             # Template for job 
    spec:                  # same as before 
      template:  # templete to make job
        spec:
          containers:
          - name: net-tools
            image: sysnet4admin/net-tools  # container image
            command: ["curlchk",  "nginx"]
          restartPolicy: Never  # restart option
{% endhighlight %}

- `successfulJobsHistoryLimit` hold the job until specific number. After the limited number, it will delete first job automatically. Default value is 3.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/40.jpg"><img src="/assets/img/posts/kubernetes_advanced/40.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

- Use `k get po | wc -l` to get total pods count.

* DaemonSet
- DaemonSet makes one pod on each nodes.

{% highlight yaml %}
apiVersion: apps/v1  # daemon set version
kind: DaemonSet  #  object type
metadata:  # information of daemon set
  labels:
    app: ds-nginx 
  name: ds-nginx
spec:  # spec of daemon set
  selector:
    matchLabels:
      app: po-nginx 
  template:  # Template for job 
    metadata:
      labels:
        app: po-nginx
    spec:  # same as before 
      containers:
      - name: nginx 
        image: nginx # container image
{% endhighlight %}

- DaemonSet is quit simillar with deployment, but it has no replicas, because one pod can include only one DaemonSet.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/41.jpg"><img src="/assets/img/posts/kubernetes_advanced/41.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

- Use `vagrant up w4-k8s-1.22` to make fourth worker node.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/42.jpg"><img src="/assets/img/posts/kubernetes_advanced/42.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

- Use `vagrant destroy -f w4-k8s-1.22` to destroy fourth worker node.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/43.jpg"><img src="/assets/img/posts/kubernetes_advanced/43.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

- When you add node, DamonSet will be created automatically from code.

* StatefulSet
- StatefulSet saves state of pod.

{% highlight yaml %}
apiVersion: apps/v1  # StatefulSet version
kind: StatefulSet  #  object type
metadata:  # information of StatefulSet
  name: sts-chk-hn
spec:  # spec of StatefulSet
  replicas: 3
  serviceName: sts-svc-domain #statefulset need it
  selector:
    matchLabels:
      app: sts
  template:  # Template for StatefulSet 
    metadata:
      labels:
        app: sts
    spec:
      containers:
      - name: chk-hn
        image: sysnet4admin/chk-hn  # container image
{% endhighlight %}

- You should use `serviceName`, because StatefulSet has specific name, not hash value.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/44.jpg"><img src="/assets/img/posts/kubernetes_advanced/44.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

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

* command
- Use command in yaml file to run specific command.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/28.jpg"><img src="/assets/img/posts/kubernetes_advanced/28.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

* multiple commands
- Use `&&` to run multiple commands at once.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/29.jpg"><img src="/assets/img/posts/kubernetes_advanced/29.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

- Use `;` to run multiple commans step by step.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/30.jpg"><img src="/assets/img/posts/kubernetes_advanced/30.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

- Use `|` to separate command lines.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/31.jpg"><img src="/assets/img/posts/kubernetes_advanced/31.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

- Use `arg` to separate config and commands.

# Expose Deployed Application
- We don't use HostPort and HostNetwork, because we should know where the pods is running.

## Port-forward
- We have host port and guest port. When we use host port, then this host port will be changed to guest port.
- For example, our host port is 60010, and when we connect to 60010, then our master node will change 60010 to 22 and connect to 22.

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: fwd-chk-hn
spec:
  containers:
  - name: chk-hn 
    image: sysnet4admin/chk-hn
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/45.jpg"><img src="/assets/img/posts/kubernetes_advanced/45.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

- `k port-forward fwd-chk-hn 80:80` means, we will open 80 with specific address and it will be changed to 80.
- `k port-forward --address 0.0.0.0 fwd-chk-hn 80:80` means, we will open 80 with all address and it will be changed to 80.

## HostPort
- Outside users should know, which node they will connect.
- For example, 8080 is second worker node host port, and it will change 8080 to 80.

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: hp-chk-hn 
spec:
  containers:
  - name: chk-hn
    image: sysnet4admin/chk-hn
    ports:
    - containerPort: 80
      hostPort: 8080
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/46.jpg"><img src="/assets/img/posts/kubernetes_advanced/46.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

## HostNetwork
- Outside users should know, which node they will connect. And they connect directly that port.

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: hnet-chk-hn
spec:
  hostNetwork: true
  containers:
    - name: chk-hn
      image: sysnet4admin/chk-hn 
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/47.jpg"><img src="/assets/img/posts/kubernetes_advanced/47.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

## NodePort

{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-nginx
  labels:
    app: deploy-nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: deploy-nginx
  template:
    metadata:
      labels:
        app: deploy-nginx 
    spec:
      containers:
      - name: nginx
        image: nginx
---  # separator for objects
apiVersion: v1
kind: Service
metadata:
  name: np-nginx 
spec:
  selector:
    app: deploy-nginx  # deployment to expose
  ports:
    - name: http
      port: 80  # service
      targetPort: 80  # pod
      nodePort: 30000 #option 
  type: NodePort # using NodePort
{% endhighlight %}

- User will connect 30000 node and node will connect to 80 service and service will connect to 80 pod.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/48.jpg"><img src="/assets/img/posts/kubernetes_advanced/48.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

## LoadBalancer
- We will use matalib instead of NodePort.

{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-nginx
  labels:
    app: deploy-nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: deploy-nginx
  template:
    metadata:
      labels:
        app: deploy-nginx 
    spec:
      containers:
      - name: nginx
        image: nginx
---
apiVersion: v1
kind: Service
metadata:
  name: lb-nginx 
spec:
  selector:
    app: deploy-nginx  
  ports:
    - name: http
      port: 80
      targetPort: 80 
  type: LoadBalancer
{% endhighlight %}

{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-chk-ip
  labels:
    app: deploy-chk-ip
spec:
  replicas: 3
  selector:
    matchLabels:
      app: deploy-chk-ip
  template:
    metadata:
      labels:
        app: deploy-chk-ip 
    spec:
      containers:
      - name: chk-ip
        image: sysnet4admin/chk-ip
---
apiVersion: v1
kind: Service
metadata:
  name: lb-chk-ip 
spec:
  selector:
    app: deploy-chk-ip  
  ports:
    - name: http
      port: 80
      targetPort: 80 
  type: LoadBalancer
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/49.jpg"><img src="/assets/img/posts/kubernetes_advanced/49.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

## ExternalName
- ExternalName has no Deployment because it uses external name to service.
- ExternalName is matched metadata's name.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/50.jpg"><img src="/assets/img/posts/kubernetes_advanced/50.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

{% highlight yaml %}
apiVersion: v1
kind: Service
metadata:
  name: ex-url-1 
  namespace: default
spec:
  type: ExternalName 
  externalName: sysnet4admin.github.io # External Domain Name
{% endhighlight %}

{% highlight yaml %}
apiVersion: v1
kind: Service
metadata:
  name: ex-url-2 
  namespace: default
spec:
  type: ExternalName
  externalName: k8s-edu.github.io # changable as you want 
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/51.jpg"><img src="/assets/img/posts/kubernetes_advanced/51.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

## ClusterIP
- ClusterIP exposes Deployment or Pod.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/52.jpg"><img src="/assets/img/posts/kubernetes_advanced/52.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>


{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-nginx
  labels:
    app: deploy-nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: deploy-nginx
  template:
    metadata:
      labels:
        app: deploy-nginx 
    spec:
      containers:
      - name: nginx
        image: nginx
---
apiVersion: v1
kind: Service
metadata:
  name: cl-nginx 
spec:
  selector:
    app: deploy-nginx  
  ports:  # ClusterIP has port and target port to connect each other. 
    - name: http
      port: 80
      targetPort: 80
  type: ClusterIP
{% endhighlight %}


## Headless
- Headless exposes Deployment or Pod without IP.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/53.jpg"><img src="/assets/img/posts/kubernetes_advanced/53.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-nginx
  labels:
    app: deploy-nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: deploy-nginx
  template:
    metadata:
      labels:
        app: deploy-nginx 
    spec:
      containers:
      - name: nginx
        image: nginx
---
apiVersion: v1
kind: Service
metadata:
  name: hdl-nginx 
spec:
  selector:
    app: deploy-nginx  
  ports:
    - name: http
      port: 80
      targetPort: 80
  clusterIP: None # Headless has no type and no IP.
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/54.jpg"><img src="/assets/img/posts/kubernetes_advanced/54.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

- Headless can communicate with domain name, without IP and connect to StateFulset with domain name.
- StateFulset matches service Name to connect Headless.
- When you use StateFulset with LoadBalancer, each External IP calls can show different pods. Therefore, I recommend, using StateFulset with Headless.

{% highlight yaml %}
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: sts-chk-hn
spec:
  replicas: 3
  serviceName: sts-svc-domain #statefulset need it
  selector:
    matchLabels:
      app: sts
  template:
    metadata:
      labels:
        app: sts
    spec:
      containers:
      - name: chk-hn
        image: sysnet4admin/chk-hn
---
apiVersion: v1
kind: Service
metadata:
  name: sts-svc-domain
spec:
  selector:
    app: sts
  ports:
    - port: 80
  clusterIP: None
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/55.jpg"><img src="/assets/img/posts/kubernetes_advanced/55.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

## EndPoint
- When you create Deployment and LoadBalancer together, EndPoint is also created.

{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-chk-ip
  labels:
    app: deploy-chk-ip
spec:
  replicas: 3
  selector:
    matchLabels:
      app: deploy-chk-ip
  template:
    metadata:
      labels:
        app: deploy-chk-ip 
    spec:
      containers:
      - name: chk-ip
        image: sysnet4admin/chk-ip
---
apiVersion: v1
kind: Service
metadata:
  name: lb-chk-ip 
spec:
  selector:
    app: deploy-chk-ip  
  ports:
    - name: http
      port: 80
      targetPort: 80 
  type: LoadBalancer
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/56.jpg"><img src="/assets/img/posts/kubernetes_advanced/56.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

- You can create EndPoint independently.
- Create Service first as ClusterIP and create also EndPoint with service name and LoadBalancer IP. As a result, you can call EndPoint with service name and EndPoint is binded with LoadBalancer IP, like double binding.

{% highlight yaml %}
apiVersion: v1
kind: Service
metadata:
  name: external-data 
spec:
  ports:
    - name: http
      port: 80
      targetPort: 80
---
apiVersion: v1
kind: Endpoints
metadata:
  name: external-data # match name with service.
subsets:
  - addresses:
      - ip: 192.168.1.11 # LoadBalancer IP
    ports:
      - name: http
        port: 80
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/57.jpg"><img src="/assets/img/posts/kubernetes_advanced/57.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

## Ingress
- Ingress cannot exist without service.
- Ingress has routing information and service routs the app. 

<figure class="half">
  <a href="/assets/img/posts/kubernetes_advanced/58.jpg"><img src="/assets/img/posts/kubernetes_advanced/58.jpg"></a>
  <a href="/assets/img/posts/kubernetes_advanced/59.jpg"><img src="/assets/img/posts/kubernetes_advanced/59.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

* Services

- deploy-nginx
{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-nginx
  labels:
    app: deploy-nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: deploy-nginx
  template:
    metadata:
      labels:
        app: deploy-nginx 
    spec:
      containers:
      - name: nginx
        image: nginx
---
apiVersion: v1
kind: Service
metadata:
  name: ing-default 
spec:
  selector:
    app: deploy-nginx  
  ports:
    - name: http
      port: 80
      targetPort: 80
  type: ClusterIP
{% endhighlight %}

- deploy-hn
{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-hn
  labels:
    app: deploy-hn
spec:
  replicas: 3
  selector:
    matchLabels:
      app: deploy-hn
  template:
    metadata:
      labels:
        app: deploy-hn
    spec:
      containers:
      - name: chk-hn
        image: sysnet4admin/chk-hn
---
apiVersion: v1
kind: Service
metadata:
  name: ing-hn 
spec:
  selector:
    app: deploy-hn  
  ports:
    - name: http
      port: 80
      targetPort: 80
  type: ClusterIP
{% endhighlight %}

- deploy-ip
{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-ip
  labels:
    app: deploy-ip
spec:
  replicas: 3
  selector:
    matchLabels:
      app: deploy-ip
  template:
    metadata:
      labels:
        app: deploy-ip
    spec:
      containers:
      - name: chk-ip
        image: sysnet4admin/chk-ip
---
apiVersion: v1
kind: Service
metadata:
  name: ing-ip 
spec:
  selector:
    app: deploy-ip  
  ports:
    - name: http
      port: 80
      targetPort: 80
  type: ClusterIP
{% endhighlight %}

* Ingress
{% highlight yaml %}
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress
  annotations: # set default path
    kubernetes.io/ingress.class: "nginx"
spec:
  rules:
  - http:
      paths:
      - path: / 
        pathType: Prefix
        backend:
          service:
            name: ing-default
            port:
              number: 80
      - path: /hn
        pathType: Prefix
        backend:
          service:
            name: ing-hn
            port:
              number: 80
      - path: /ip 
        pathType: Prefix
        backend:
          service:
            name: ing-ip
            port:
              number: 80
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/61.jpg"><img src="/assets/img/posts/kubernetes_advanced/61.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

- with NodePort

<figure class="half">
  <a href="/assets/img/posts/kubernetes_advanced/62.jpg"><img src="/assets/img/posts/kubernetes_advanced/62.jpg"></a>
  <a href="/assets/img/posts/kubernetes_advanced/63.jpg"><img src="/assets/img/posts/kubernetes_advanced/63.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

- with LoadBalancer

<figure class="half">
  <a href="/assets/img/posts/kubernetes_advanced/64.jpg"><img src="/assets/img/posts/kubernetes_advanced/64.jpg"></a>
  <a href="/assets/img/posts/kubernetes_advanced/65.jpg"><img src="/assets/img/posts/kubernetes_advanced/65.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

### Label vs Annotation
- Label is for human and annotation is for system.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/60.jpg"><img src="/assets/img/posts/kubernetes_advanced/60.jpg"></a>
  <figcaption>Definitions</figcaption>
</figure>

# Volume

## emptyDir
- emptyDir is shared memory in pods.

{% highlight yaml %}
apiVersion: v1
kind: Pod # make emptyDir as a pod
metadata:
  name: pod-emptydir 
  labels:
    app: nginx 
spec:
  containers:
  - name: web-page # make container 1
    image: nginx 
    volumeMounts:
    - mountPath: /usr/share/nginx/html # container 1 first page
      name: empty-directory 

  - name: html-builder # make container 2
    image: alpine 
    volumeMounts:
    - mountPath: /html-dir # container 2 first page is /html-dir/index.html
      name: empty-directory 
    command: ["/bin/sh", "-c"]
    args: 
      - echo "This page created on $(date +%Y-%m-%d)" > /html-dir/index.html;
        sleep infinity;
        
  volumes:
  - name: empty-directory 
    emptyDir: {}
{% endhighlight %}

- In this example, we will create 2 containers. 
- After creating each of first page, this first page gonna be empty-directory. 
- This created empty-directory will be a volume and this volume connects 2 containers. 

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/68.jpg"><img src="/assets/img/posts/kubernetes_advanced/68.jpg"></a>
  <figcaption>Volume</figcaption>
</figure>

- We called container 2 with IP but actual running pod is container 1.

## hostPath
- hostPath can use Node directories.

{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-hostpath
  labels:
    app: deploy-hostpath
spec:
  replicas: 3
  selector:
    matchLabels:
      app: deploy-hostpath 
  template:
    metadata:
      labels:
        app: deploy-hostpath 
    spec:
      containers:
      - name: host-mon
        image: sysnet4admin/sleepy
        volumeMounts:
        - mountPath: /host-log  
          name: hostpath-directory 
      volumes:
      - name: hostpath-directory 
        hostPath:
          path: /var/log
{% endhighlight %}

- It connects `/var/log` and `/host-log` with name `hostpath-directory`.
- When Deployment is released on Node, Node can take diffrent amounts of Deployments.
- So it's difficult to make a hostPath on one Node.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/69.jpg"><img src="/assets/img/posts/kubernetes_advanced/69.jpg"></a>
  <figcaption>Volume</figcaption>
</figure>

{% highlight yaml %}
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: ds-hostpath
  labels:
    app: ds-hostpath
spec:
  selector:
    matchLabels:
      app: ds-hostpath 
  template:
    metadata:
      labels:
        app: ds-hostpath 
    spec:
      containers:
      - name: host-mon
        image: sysnet4admin/sleepy
        volumeMounts:
        - mountPath: /host-log  
          name: hostpath-directory 
      volumes:
      - name: hostpath-directory 
        hostPath:
          path: /var/log
{% endhighlight %}

- DaemonSet is created on one Node. It means our hostPath is separated fairly on Nodes.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/70.jpg"><img src="/assets/img/posts/kubernetes_advanced/70.jpg"></a>
  <figcaption>Volume</figcaption>
</figure>

## NFS
- Network File System

{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-nfs
  labels:
    app: deploy-nfs
spec:
  replicas: 3
  selector:
    matchLabels:
      app: deploy-nfs
  template:
    metadata:
      labels:
        app: deploy-nfs
    spec:
      containers:
      - name: chk-log 
        image: sysnet4admin/chk-log
        volumeMounts:
        - name: nfs-vol
          mountPath: /audit
      volumes:
      - name: nfs-vol
        nfs:
          server: 192.168.1.10
          path: /nfs_shared/nfs-vol
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/71.jpg"><img src="/assets/img/posts/kubernetes_advanced/71.jpg"></a>
  <figcaption>Volume</figcaption>
</figure>

## PV & PVC

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/72.jpg"><img src="/assets/img/posts/kubernetes_advanced/72.jpg"></a>
  <figcaption>Volume</figcaption>
</figure>

* accessModes
- ReadWriteOnce(RWO) : Read and Write on only one node.
- ReadOnlyMany(ROX) : Read on several nodes.
- ReadWriteMany(RWX) : Read and Write on several nodes.
- Block Storage uses RWO and ROX and Object Storage uses RWX.

{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-pvc
  labels:
    app: deploy-pvc 
spec:
  replicas: 3
  selector:
    matchLabels:
      app: deploy-pvc
  template:
    metadata:
      labels:
        app: deploy-pvc
    spec:
      containers:
      - name: chk-log 
        image: sysnet4admin/chk-log
        volumeMounts:
        - name: pvc-vol
          mountPath: /audit
      volumes:
      - name: pvc-vol
        persistentVolumeClaim:
          claimName: pvc-nfs # created pvc name
{% endhighlight %}

### PV
- Persistent Volume

{% highlight yaml %}
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs 
spec:
  capacity:
    storage: 100Mi
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  nfs:
    server: 192.168.1.10
    path: /nfs_shared/pvc-vol
{% endhighlight %}

* persistentVolumeReclaimPolicy
- Retain : retain PV even you delete PVC
- Delete : delete PV when you delete PVC

### PVC
- Persistent Volume Claim

{% highlight yaml %}
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs  
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 10Mi
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/73.jpg"><img src="/assets/img/posts/kubernetes_advanced/73.jpg"></a>
  <figcaption>Volume</figcaption>
</figure>

## StorageClass
- Persitent Volume Claim make StorageClass first, and then StorageClass make a Persistent Volume.

<figure class="third">
  <a href="/assets/img/posts/kubernetes_advanced/66.jpg"><img src="/assets/img/posts/kubernetes_advanced/66.jpg"></a>
  <a href="/assets/img/posts/kubernetes_advanced/74.jpg"><img src="/assets/img/posts/kubernetes_advanced/74.jpg"></a>
  <a href="/assets/img/posts/kubernetes_advanced/76.jpg"><img src="/assets/img/posts/kubernetes_advanced/76.jpg"></a>
  <figcaption>Volume</figcaption>
</figure>

* Provisioning
- Static : nfs, PV & PVC
- Dynamic : StorageClass
- In nfs mode, administrator should create pv everytime when it is requested.
- In PV & PVC mode, administrator should prepare always PV before user uses pvc.
- In StorageClass mode, administrator don't need to prepare pvc before, because StorageClass will create PVC automatically everytime when it is requested.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/75.jpg"><img src="/assets/img/posts/kubernetes_advanced/75.jpg"></a>
  <figcaption>Volume</figcaption>
</figure>

* provisioner
{% highlight yaml %}
kind: Deployment
apiVersion: apps/v1
metadata:
  name: nfs-client-provisioner
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nfs-client-provisioner
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: nfs-client-provisioner
    spec:
      serviceAccountName: nfs-client-provisioner
      containers:
        - name: nfs-client-provisioner
          image: k8s.gcr.io/sig-storage/nfs-subdir-external-provisioner:v4.0.2
          volumeMounts:
            - name: nfs-client-root
              mountPath: /persistentvolumes
          env:
            - name: PROVISIONER_NAME
              value: k8s-sigs.io/nfs-subdir-external-provisioner
            - name: NFS_SERVER # NFS Server
              value: 192.168.1.10
            - name: NFS_PATH
              value: /nfs_shared/dynamic-vol
      volumes:
        - name: nfs-client-root
          nfs:
            server: 192.168.1.10 # NFS Client Root
            path: /nfs_shared/dynamic-vol
{% endhighlight %}

- NFS server and NFS client Root should have same values.

* StorageClass
{% highlight yaml %}
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-nfs-storage
# or choose another name, must match deployment's env PROVISIONER_NAME'
provisioner: k8s-sigs.io/nfs-subdir-external-provisioner 
parameters:
  # waits for nfs.io/storage-path annotation, if not specified will accept as empty string.
  pathPattern: "${.PVC.namespace}/${.PVC.annotations.nfs.io/storage-path}" 
  onDelete: delete
{% endhighlight %}

* PVC
{% highlight yaml %}
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-dynamic  
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 10Gi
  storageClassName: managed-nfs-storage # name of StorageClass
{% endhighlight %}

* Deployment
{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-pvc 
  labels:
    app: deploy-pvc 
spec:
  replicas: 3
  selector:
    matchLabels:
      app: deploy-pvc
  template:
    metadata:
      labels:
        app: deploy-pvc
    spec:
      containers:
      - name: chk-log 
        image: sysnet4admin/chk-log
        volumeMounts:
        - name: pvc-vol
          mountPath: /audit
      volumes:
      - name: pvc-vol
        persistentVolumeClaim:
          claimName: pvc-dynamic # PVC name 
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/kubernetes_advanced/77.jpg"><img src="/assets/img/posts/kubernetes_advanced/77.jpg"></a>
  <a href="/assets/img/posts/kubernetes_advanced/78.jpg"><img src="/assets/img/posts/kubernetes_advanced/89.jpg"></a>
  <figcaption>Volume</figcaption>
</figure>

## vol
- volumeClaimTemplates
- This is a volume type only for StateFullSet.
- StateFullSet has Status and independent Domain (with Headless). It means, StateFullSet accesses Pod independently.
- Therefore, each pod has unique value and status.
- When this pod claims volumeClaimTemplates, it creates independent PV.
- As a result, When you deploy with StateFullSet, your Pod has independent Domain, volumeClaimTemplates and PV.

<figure class="third">
  <a href="/assets/img/posts/kubernetes_advanced/67.jpg"><img src="/assets/img/posts/kubernetes_advanced/67.jpg"></a>
  <a href="/assets/img/posts/kubernetes_advanced/79.jpg"><img src="/assets/img/posts/kubernetes_advanced/79.jpg"></a>
  <a href="/assets/img/posts/kubernetes_advanced/80.jpg"><img src="/assets/img/posts/kubernetes_advanced/80.jpg"></a>
  <figcaption>Volume</figcaption>
</figure>

{% highlight yaml %}
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: sts
spec:
  replicas: 3
  serviceName: sts-svc-domain #statefulset need it
  selector:
    matchLabels:
      app: sts
  template:
    metadata:
      labels:
        app: sts
    spec:
      containers:
      - name: chk-hn
        image: sysnet4admin/chk-hn
        volumeMounts:
        - name: each-sts-backup # should be same with vol
          mountPath: /backup_data
  volumeClaimTemplates:
  - metadata:
      name: each-sts-backup
    spec:
      accessModes: [ "ReadWriteOnce" ] # cause it takes only one Pod
      storageClassName: "managed-nfs-storage"
      resources:
        requests:
          storage: 20Gi
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/kubernetes_advanced/81.jpg"><img src="/assets/img/posts/kubernetes_advanced/81.jpg"></a>
  <a href="/assets/img/posts/kubernetes_advanced/82.jpg"><img src="/assets/img/posts/kubernetes_advanced/82.jpg"></a>
  <figcaption>Volume</figcaption>
</figure>

# Node Contributions and Management

## Cordon

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/83.jpg"><img src="/assets/img/posts/kubernetes_advanced/83.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

- When you use cordon in specific node, that node is not affected from scheduling.

<figure class="half">
  <a href="/assets/img/posts/kubernetes_advanced/85.jpg"><img src="/assets/img/posts/kubernetes_advanced/85.jpg"></a>
  <a href="/assets/img/posts/kubernetes_advanced/86.jpg"><img src="/assets/img/posts/kubernetes_advanced/86.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

- w3-k8s is not updated, cause we set it with cordon.

## Drain

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/84.jpg"><img src="/assets/img/posts/kubernetes_advanced/84.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

- Drain moves original pod to other nodes and set cordon on the node. 
- Set drain on pod to maintanance or when the pod can occur some error.

{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-drain 
spec:
  replicas: 3
  selector:
    matchLabels:
      app: deploy-drain
  template:
    metadata:
      labels:
        app: deploy-drain
    spec:
      containers:
      - name: nginx  
        image: nginx 
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/87.jpg"><img src="/assets/img/posts/kubernetes_advanced/87.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

- At first, you will get this error to drain, because daemonset pod cannot be deleted.
- So you should use `--ignore-daemonsets --force`

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/88.jpg"><img src="/assets/img/posts/kubernetes_advanced/88.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

- And now you can see, we missed one pod `net`.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/89.jpg"><img src="/assets/img/posts/kubernetes_advanced/89.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

## nodeName

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/90.jpg"><img src="/assets/img/posts/kubernetes_advanced/90.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

- Use nodeName to set where your pod should be deployed.

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: nodename 
spec:
  containers:
  - name: nginx
    image: nginx
  nodeName: w3-k8s # set nodeName where pod be deployed
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/91.jpg"><img src="/assets/img/posts/kubernetes_advanced/91.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

# nodeLabel

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/92.jpg"><img src="/assets/img/posts/kubernetes_advanced/92.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

- With nodeLabel and nodeSelector you can release several pods at once. 

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/93.jpg"><img src="/assets/img/posts/kubernetes_advanced/93.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

- Use `k get node --show-labels` to see labels of nodes
- Use `k label node [node] [label]` to add label on specific node

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/94.jpg"><img src="/assets/img/posts/kubernetes_advanced/94.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

- Use `k get node -l [label]` to search nodes with label
- `=` symbol on label seperate key(left) value(right) and of course you can use only one when you search.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/95.jpg"><img src="/assets/img/posts/kubernetes_advanced/95.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

- Use `k label node [node] [label]-` to delete label on node

{% highlight sh %}
#!/usr/bin/env bash

kubectl label node w1-k8s gpupool=nvidia accelerator=tesla-a100
kubectl label node w2-k8s gpupool=nvidia accelerator=tesla-v100
kubectl label node w3-k8s diskint=nvme inmemory=redis
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/96.jpg"><img src="/assets/img/posts/kubernetes_advanced/96.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

## nodeSelector

- Use nodeSelector to set, in which node the pod should be deployed

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/97.jpg"><img src="/assets/img/posts/kubernetes_advanced/97.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: nodeselector-inmemory
spec:
  containers:
  - name: nginx
    image: nginx
  nodeSelector:
    inmemory: redis # w3-k8s
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/98.jpg"><img src="/assets/img/posts/kubernetes_advanced/98.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: nodeselector-gpupool
spec:
  containers:
  - name: nginx
    image: nginx
  nodeSelector:
    gpupool: nvidia # w1-k8s, w2-k8s
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/99.jpg"><img src="/assets/img/posts/kubernetes_advanced/99.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

# nodeAffinity

- Use nodeAffinity to set more felexible confitions.
- There is two options to set: requiredDuringSchedulingIgnoredDuringExecution and preferredDuringSchedulingIgnoredDuringExecution
- Operators: 
  * In vs NotIn
  * Exists vs DoesNotExist
  * Gt vs Lt

{% highlight yaml %}
apiVersion: v1
kind: Pod
metadata:
  name: nodeaffinity
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions: # inmemory-redis
          - key: inmemory
            operator: In
            values:
            - redis
  containers:
  - name: nginx
    image: nginx
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/100.jpg"><img src="/assets/img/posts/kubernetes_advanced/100.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nodeaffinity-preferred
  name: nodeaffinity-preferred
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nodeaffinity-preferred
  template:
    metadata:
      labels:
        app: nodeaffinity-preferred
    spec:
      containers:
      - image: nginx
        name: nginx
      affinity:
        nodeAffinity: # It will search nodes who has gpupool-nvidia and prefer node who has accelerator-tesla-a100 to release
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions: # gpupool-nvidia
              - key: gpupool
                operator: In
                values:
                - nvidia
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 1 # higher means more affinity
            preference:
              matchExpressions: # accelerator-tesla-a100
              - key: accelerator
                operator: In
                values:
                - tesla-a100
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/101.jpg"><img src="/assets/img/posts/kubernetes_advanced/101.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: anti-nodeaffinity
  name: anti-nodeaffinity
spec:
  replicas: 3
  selector:
    matchLabels:
      app: anti-nodeaffinity
  template:
    metadata:
      labels:
        app: anti-nodeaffinity
    spec:
      containers:
      - image: nginx
        name: nginx
      affinity:
        nodeAffinity: # It will search nodes who has gpupool-nvidia and unprefer node who has accelerator-tesla-a100 to release
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions: # gpupool-nvidia
              - key: gpupool
                operator: In
                values:
                - nvidia
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 1
            preference:
              matchExpressions:
              - key: accelerator # accelerator-tesla-a100 is unpreferred
                operator: NotIn
                values:
                - tesla-a100
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/102.jpg"><img src="/assets/img/posts/kubernetes_advanced/102.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

# Taints & Tolerations

<figure class="half">
  <a href="/assets/img/posts/kubernetes_advanced/103.jpg"><img src="/assets/img/posts/kubernetes_advanced/103.jpg"></a>
  <a href="/assets/img/posts/kubernetes_advanced/112.jpg"><img src="/assets/img/posts/kubernetes_advanced/112.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

- Effect
  * NoSchedule : Only deploy with Telerations
  * PreferNoSchedule : When there is no more nodes to deploy, ignore Taints setting
  * NoExecute : Reschedule and delete pods which has no telerations

{% highlight yaml %}
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: daemonset-w-tolerations
  labels:
    app: daemonset-w-tolerations
spec:
  selector:
    matchLabels:
      app: daemonset-w-tolerations
  template:
    metadata:
      labels:
        app: daemonset-w-tolerations
    spec:
      containers:
      - name: nginx
        image: nginx
      tolerations:
      - effect: NoSchedule
        key: node-role.kubernetes.io/master
{% endhighlight %}

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/104.jpg"><img src="/assets/img/posts/kubernetes_advanced/104.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

- When key has kaster, then DaemonSet can be deployed in master also.

{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: deploy-after-taints
  name: deploy-after-taints
spec:
  replicas: 6
  selector:
    matchLabels:
      app: deploy-after-taints
  template:
    metadata:
      labels:
        app: deploy-after-taints
    spec:
      containers:
      - image: nginx
        name: nginx
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/kubernetes_advanced/105.jpg"><img src="/assets/img/posts/kubernetes_advanced/105.jpg"></a>
  <a href="/assets/img/posts/kubernetes_advanced/106.jpg"><img src="/assets/img/posts/kubernetes_advanced/106.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

- Pod cannot be deployed on w3-k8s, because w3-k8s has taint and pod has no teleration.

{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: deploy-w-tolerations
  name: deploy-w-tolerations
spec:
  replicas: 6
  selector:
    matchLabels:
      app: deploy-w-tolerations
  template:
    metadata:
      labels:
        app: deploy-w-tolerations
    spec:
      containers:
      - image: nginx
        name: nginx
      tolerations: # it has telerations
      - effect: NoSchedule
        key: DB
        value: customer-info
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/kubernetes_advanced/107.jpg"><img src="/assets/img/posts/kubernetes_advanced/107.jpg"></a>
  <a href="/assets/img/posts/kubernetes_advanced/108.jpg"><img src="/assets/img/posts/kubernetes_advanced/108.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

- Pod cab be deployed on w1-k8s, w2-k8s and w3-k8s, because pod has teleration.

{% highlight yaml %}
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: deploy-w-tolerations-nodeaffinity
  name: deploy-w-tolerations-nodeaffinity
spec:
  replicas: 6
  selector:
    matchLabels:
      app: deploy-w-tolerations-nodeaffinity
  template:
    metadata:
      labels:
        app: deploy-w-tolerations-nodeaffinity
    spec:
      containers:
      - image: nginx
        name: nginx
      tolerations:
      - effect: NoSchedule
        key: DB
        value: customer-info
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions: # it prefer node who has inmemory-redis label
              - key: inmemory
                operator: In
                values:
                - redis
{% endhighlight %}

<figure class="half">
  <a href="/assets/img/posts/kubernetes_advanced/109.jpg"><img src="/assets/img/posts/kubernetes_advanced/109.jpg"></a>
  <a href="/assets/img/posts/kubernetes_advanced/110.jpg"><img src="/assets/img/posts/kubernetes_advanced/110.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>

{% highlight sh %}
#!/usr/bin/env bash

kubectl patch node w1-k8s -p '{"spec":{"taints":[]}}'
kubectl patch node w2-k8s -p '{"spec":{"taints":[]}}'
kubectl patch node w3-k8s -p '{"spec":{"taints":[]}}'

CODE=$(kubectl get node -o yaml |  grep -i taints | wc -l) # Check status of taints

echo "successfully init taints in the k8s cluster"
echo "Result code is $CODE"
echo "if Result is not 1, please reload all of worker nodes"
{% endhighlight %}

- Above code is for deleting taints on nodes.
- Or you can jsut rerun nodes to delte taints.

<figure>
  <a href="/assets/img/posts/kubernetes_advanced/111.jpg"><img src="/assets/img/posts/kubernetes_advanced/111.jpg"></a>
  <figcaption>Node Contributions and Management</figcaption>
</figure>
