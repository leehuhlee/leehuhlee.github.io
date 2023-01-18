---
layout: post
title: "[Kubernetes] Kubernetes Basic"
date: 2022-12-29
excerpt: "Kubernetes"
tags: [Kubernetes, Server, Docker]
comments: false
---

# IDE

## Download

  - Download <a href="https://www.virtualbox.org/wiki/Downloads">Virtual Box</a>.
  - Download <a href="https://developer.hashicorp.com/vagrant/downloads">Vagrant</a>.
  - Download <a href="https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html">Putty</a>.
  - Download <a href="https://github.com/jimradford/superputty/releases/tag/1.4.10">SuperPutty</a>.
  - Download <a href="https://github.com/sysnet4admin/_Lecture_k8s_starter.kit">Kubernetes Starter Kit</a>.

## Run Kubernetes

  - Open `~\_Lecture_k8s_starter.kit-main\ch1\1.2\k8s-min-5GiB` on `commend`
  - Enter `vagrant up`

<figure>
  <a href="/assets/img/posts/kubernetes_basic/0.jpg"><img src="/assets/img/posts/kubernetes_basic/0.jpg"></a>
  <figcaption>IDE</figcaption>
</figure>

  - Open Virtual Box to check 4 machines 

<figure>
  <a href="/assets/img/posts/kubernetes_basic/1.jpg"><img src="/assets/img/posts/kubernetes_basic/1.jpg"></a>
  <figcaption>IDE</figcaption>
</figure>

### Run Kubernetes on Terminal

  - Open SuperPutty - [File] - [Import Sessions] - Import `~\_Lecture_k8s_starter.kit-main\ch1\1.3\Sessions.Xml`

<figure>
  <a href="/assets/img/posts/kubernetes_basic/2.jpg"><img src="/assets/img/posts/kubernetes_basic/2.jpg"></a>
  <figcaption>IDE</figcaption>
</figure>

## Setting

  - To easy exit from the virtual machine, Open Virtual Box - [Environment Settings] - [Input] - [Virtual Machine] - Click Host Key and Press Ctrl + Alt
  - To change font size of SuperPutty, Open SuperPutty and Click m-k8s from Sessions - Right click the session name in Commands - [Change Settings] - [Window] - [Apperance]

<figure class="half">
  <a href="/assets/img/posts/kubernetes_basic/2.jpg"><img src="/assets/img/posts/kubernetes_basic/2.jpg"></a>
  <a href="/assets/img/posts/kubernetes_basic/3.jpg"><img src="/assets/img/posts/kubernetes_basic/3.jpg"></a>
  <figcaption>IDE</figcaption>
</figure>

# Pod

  - Pod is union of containers.
  - One pod has one or several containers to do one specific work.

## Release Pod with NGINX

  - Enter `kubectl run nginx --image=nginx`
  - Enter `Kubectl get pod` and wait until Status is changed to Running
  - Enter `Kubectl get pod -o wode` and copy nginx IP
  - Enter `curl [nginx IP]`

<figure>
  <a href="/assets/img/posts/kubernetes_basic/4.jpg"><img src="/assets/img/posts/kubernetes_basic/4.jpg"></a>
  <figcaption>Pod</figcaption>
</figure>

## Connect Pod from Outside

### Service

  - We use a service to connect a pod from Outside.
  - At first, service will be connected with a NodePort.
  - Then the NodePort communicates with the node.
  - As a result, the service finds the pod in the node.

### Expose Service

  - Enter `kubectl expose pod nginx --type=NodePort --port=80` to expose a service with a port.
  - Enter `kubectl get service` and check your port number.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/5.jpg"><img src="/assets/img/posts/kubernetes_basic/5.jpg"></a>
  <figcaption>Pod</figcaption>
</figure>

  - Enter `kubectl get nodes -o wide` and check INTERNAL-IP.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/6.jpg"><img src="/assets/img/posts/kubernetes_basic/6.jpg"></a>
  <figcaption>Pod</figcaption>
</figure>

  - Check one of IP is connected from outside like commands or web browser.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/7.jpg"><img src="/assets/img/posts/kubernetes_basic/7.jpg"></a>
  <figcaption>Pod</figcaption>
</figure>

## Deployment

  - Union of Pods

## Create Deployment

  - Enter `kubectl create deployment deploy-nginx --image=nginx`.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/8.jpg"><img src="/assets/img/posts/kubernetes_basic/8.jpg"></a>
  <figcaption>Pod</figcaption>
</figure>

## Scale Deployment

  - Enter `kubectl scale deployment deploy-nginx --replicas=3` because default replicas set is 1.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/9.jpg"><img src="/assets/img/posts/kubernetes_basic/9.jpg"></a>
  <figcaption>Pod</figcaption>
</figure>

## Expose Deployment

  - This way is not the best way to connect with pods because you have to share your Internal IP. 
  - Enter `kubectl expose deployment deploy-nginx --type=NodePort --port=80`.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/10.jpg"><img src="/assets/img/posts/kubernetes_basic/10.jpg"></a>
  <figcaption>Pod</figcaption>
</figure>

## Load Balancer

  - We use Load Balancer to expose the Deployment with External IP.
  - Enter `kubectl apply -f ~/_Lecture_k8s_starter.kit/ch2/2.4/metallb.yaml` to install MetaLib.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/11.jpg"><img src="/assets/img/posts/kubernetes_basic/11.jpg"></a>
  <figcaption>Pod</figcaption>
</figure>

  - Enter `kubectl create deployment chk-hn --image=sysnet4admin/chk-hn`.
  - Enter `kubectl scale deployment chk-hn --replicas=3`.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/12.jpg"><img src="/assets/img/posts/kubernetes_basic/12.jpg"></a>
  <figcaption>Pod</figcaption>
</figure>

  - Enter `kubectl expose deployment chk-hn --type=LoadBalancer --port=80`.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/13.jpg"><img src="/assets/img/posts/kubernetes_basic/13.jpg"></a>
  <figcaption>Pod</figcaption>
</figure>

  - Check your External IP with web browser

<figure>
  <a href="/assets/img/posts/kubernetes_basic/14.jpg"><img src="/assets/img/posts/kubernetes_basic/14.jpg"></a>
  <figcaption>Pod</figcaption>
</figure>

## Delete Deployment, Service, Pod and MetaLib

  - Enter `kubectl delete deployment [Your Deployment Name]`.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/15.jpg"><img src="/assets/img/posts/kubernetes_basic/15.jpg"></a>
  <figcaption>Pod</figcaption>
</figure>

  - Enter `kubectl delete service [Your Service Name]`.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/16.jpg"><img src="/assets/img/posts/kubernetes_basic/16.jpg"></a>
  <figcaption>Pod</figcaption>
</figure>

  - Enter `kubectl delete Pod [Your Pod Name]`.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/17.jpg"><img src="/assets/img/posts/kubernetes_basic/17.jpg"></a>
  <figcaption>Pod</figcaption>
</figure>

  - Enter `kubectl delete -f ~/_Lecture_k8s_starter.kit/ch2/2.4/metallb.yaml`.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/18.jpg"><img src="/assets/img/posts/kubernetes_basic/18.jpg"></a>
  <figcaption>Pod</figcaption>
</figure>

# Components

## Native components
  - Enter `kubectl get pods -n kube-system`.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/19.jpg"><img src="/assets/img/posts/kubernetes_basic/19.jpg"></a>
  <figcaption>Components</figcaption>
</figure>

### Kubernetes Cloud Service
  - EKS : Elastic Kubernetes Service from AWS 
  - AKS : Azure Kubernetes Service
  - GKE : Google Kubernetes Engine

# Issues

## About Deleteing 
  - If your deleted pod was a real pod, then you cannot rewind.
  - But if your deleted pod was in a deployment, then kubernetes rewind the pod automatically.
  - If you delete a pod in mater node, kubernetes recreate it automatically.

### Pods
  - At first, you have to make pods and a deployment for practice with `kubectl apply -f ~/_Lecture_k8s_starter.kit/ch4/4.1/`.
  - Now, you have 3 deployment's pods and 1 just pod.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/20.jpg"><img src="/assets/img/posts/kubernetes_basic/20.jpg"></a>
  <figcaption>Issues</figcaption>
</figure>

  - When you delete del-pod with `kubectl delete pod [Your Pod]`, three pods will be remained.
  - And del-pod is removed eternally.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/21.jpg"><img src="/assets/img/posts/kubernetes_basic/21.jpg"></a>
  <figcaption>Issues</figcaption>
</figure>

  - When you delete a pod which is in the deployment with `kubectl delete pod [Your Delpoyment's Pod]`, three pods will be remained.
  - Your deployment will rewind your deleted pod automatically.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/22.jpg"><img src="/assets/img/posts/kubernetes_basic/22.jpg"></a>
  <figcaption>Issues</figcaption>
</figure>

### Deployment
  - When you want to delete a pod which is in the deployment, enter `kubectl delete deployment [Your Deployment]`.
  - Your deployment will remove your all pods in that deployment.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/23.jpg"><img src="/assets/img/posts/kubernetes_basic/23.jpg"></a>
  <figcaption>Issues</figcaption>
</figure>

### Scheduler
  - To delete Scheduler in master node, enter `kubectl delete pod kube-scheduler-m-k8s -n kube-system`.
  - Then you can see that your kubernetes create new scheduler immediately.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/30.jpg"><img src="/assets/img/posts/kubernetes_basic/30.jpg"></a>
  <figcaption>Issues</figcaption>
</figure>

## About Termination
  - If you terminate a worker node, kubernetes seperate pods in that worker node to others.

### Kubelet
  - At first, terminate first worker node with `systemctl stop kubelet`

<figure>
  <a href="/assets/img/posts/kubernetes_basic/24.jpg"><img src="/assets/img/posts/kubernetes_basic/24.jpg"></a>
  <figcaption>Issues</figcaption>
</figure>

  - You have to make pods for practice with `kubectl apply -f ~/_Lecture_k8s_starter.kit/ch4/4.1/del-deploy.yaml`.
  - Now, you have 3 deployment's pods unfairly.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/25.jpg"><img src="/assets/img/posts/kubernetes_basic/25.jpg"></a>
  <figcaption>Issues</figcaption>
</figure>

  - In master node, you can also practice terminating Kubelet with `systemctl stop kubelet` and `kubectl delete pod kube-scheduler-m-k8s -n kube-system`
  - Now your scheduler in master node is always Terminating, because your master node kubelet is stopped.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/31.jpg"><img src="/assets/img/posts/kubernetes_basic/31.jpg"></a>
  <figcaption>Issues</figcaption>
</figure>

  - You can check, that kubelet in master node is still working well, with `kubectl create deployment nginx --image=nginx`

<figure>
  <a href="/assets/img/posts/kubernetes_basic/32.jpg"><img src="/assets/img/posts/kubernetes_basic/32.jpg"></a>
  <figcaption>Issues</figcaption>
</figure>

  - You can check, that scheduler in master node is still working well, with `kubectl scale deployment nginx --replicas=3`

<figure>
  <a href="/assets/img/posts/kubernetes_basic/33.jpg"><img src="/assets/img/posts/kubernetes_basic/33.jpg"></a>
  <figcaption>Issues</figcaption>
</figure>

  - By the way, to restart our kubelet and scheduler, enter `systemctl start kubelet`

<figure>
  <a href="/assets/img/posts/kubernetes_basic/34.jpg"><img src="/assets/img/posts/kubernetes_basic/34.jpg"></a>
  <figcaption>Issues</figcaption>
</figure>

### ContainerD
  - At first, terminate containerD in first worker node with `systemctl stop containerd`

<figure>
  <a href="/assets/img/posts/kubernetes_basic/26.jpg"><img src="/assets/img/posts/kubernetes_basic/26.jpg"></a>
  <figcaption>Issues</figcaption>
</figure>

  - You need to scale pods to 6 with `kubectl scale deployment del-deploy --replicas=6`.
  - Now, you have 6 deployment's pods and those pods are working in worker node 2 and 3, not 1.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/27.jpg"><img src="/assets/img/posts/kubernetes_basic/27.jpg"></a>
  <figcaption>Issues</figcaption>
</figure>

  - To restart containerD, enter `systemctl start containerd`.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/28.jpg"><img src="/assets/img/posts/kubernetes_basic/28.jpg"></a>
  <figcaption>Issues</figcaption>
</figure>

  - You have to rescale to see the worker node 1 in pod list with `kubectl scale deployment del-deploy --replicas=9`.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/29.jpg"><img src="/assets/img/posts/kubernetes_basic/29.jpg"></a>
  <figcaption>Issues</figcaption>
</figure>

  - In master node, you can also practice terminating ContainerD with `systemctl stop containerd`

<figure>
  <a href="/assets/img/posts/kubernetes_basic/35.jpg"><img src="/assets/img/posts/kubernetes_basic/35.jpg"></a>
  <figcaption>Issues</figcaption>
</figure>

  - You can check, that ContainerD in master node is still working well, with `kubectl create deployment nginx --image=nginx` and `kubectl delete deployment nginx`

<figure>
  <a href="/assets/img/posts/kubernetes_basic/36.jpg"><img src="/assets/img/posts/kubernetes_basic/36.jpg"></a>
  <figcaption>Issues</figcaption>
</figure>

  - By the way, to restart our ContainerD and scheduler, enter `systemctl start containerd` and 

<figure>
  <a href="/assets/img/posts/kubernetes_basic/37.jpg"><img src="/assets/img/posts/kubernetes_basic/37.jpg"></a>
  <figcaption>Issues</figcaption>
</figure>

### Docker
  - In worker node #1, you will stop ContainerD and Docker with `systemctl stop containerd` and `systemctl stop docker`.
  - Then your new scaled deployment is not working in worker node #1.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/58.jpg"><img src="/assets/img/posts/kubernetes_basic/58.jpg"></a>
  <figcaption>Issues</figcaption>
</figure>

  - When you stoped worker node #1 more than 5 minutes, your pods in worker node #1 are terminated.
  - But, those are not deleted, because there is no ContainerD.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/59.jpg"><img src="/assets/img/posts/kubernetes_basic/59.jpg"></a>
  <figcaption>Issues</figcaption>
</figure>

# Object
  - Most objects in kubernetes has the status and spec.
  - Pod : union of container
  - Service : Connection Pod with outside
  - Namespace : Place to publish pods, services and delpoyments
  - Volume : Place to save eternal data, like pod

## Edit Status
  - To edit status of deployment, enter `kubectl edit deployment del-deploy`

<figure class="half">
  <a href="/assets/img/posts/kubernetes_basic/38.jpg"><img src="/assets/img/posts/kubernetes_basic/38.jpg"></a>
  <a href="/assets/img/posts/kubernetes_basic/39.jpg"><img src="/assets/img/posts/kubernetes_basic/39.jpg"></a>
  <figcaption>Object</figcaption>
</figure>

## Apply Volume
  - At first, create symlink to make log file about volume with `~/_Lecture_k8s_starter.kit/ch5/5.2/nfs-exporter.sh log`, `cat /etc/exports` and `cat ~/_Lecture_k8s_starter.kit/ch5/5.2/dpy-chk-log.yaml`.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/40.jpg"><img src="/assets/img/posts/kubernetes_basic/40.jpg"></a>
  <figcaption>Object</figcaption>
</figure>

  - Now, create deployments with `kubectl apply -f ~/_Lecture_k8s_starter.kit/ch5/5.2/dpy-chk-log.yaml`.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/40.jpg"><img src="/assets/img/posts/kubernetes_basic/40.jpg"></a>
  <figcaption>Object</figcaption>
</figure>

  - Now, create deployments with `kubectl apply -f ~/_Lecture_k8s_starter.kit/ch5/5.2/dpy-chk-log.yaml`.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/41.jpg"><img src="/assets/img/posts/kubernetes_basic/41.jpg"></a>
  <figcaption>Object</figcaption>
</figure>

  - To check your log file, first of all, you have to execute a deployment with `curl [Your Deployment IP]`.
  - Then you will access this deployment with `kubectl exec dpy-chk-log-655668ffb8-jl9fr -it -- /bin/bash`.
  - And you can access the log file with `cat audit/audit_dpy-chk-log-655668ffb8-jl9fr.log`.
  - You can see your execution after that command line.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/42.jpg"><img src="/assets/img/posts/kubernetes_basic/42.jpg"></a>
  <figcaption>Object</figcaption>
</figure>

  - This log file data is eternally saved, even when we deleted deployments.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/42.jpg"><img src="/assets/img/posts/kubernetes_basic/42.jpg"></a>
  <figcaption>Object</figcaption>
</figure>

## Short Cut

| Object | Short cut | 
|---|---|
| Pod | po |
| Deployment | deploy |
| Node | no |
| Namespace | ns |
| Service | svc |

# Alias
  - With alias, we can make short cut in kubernetes.

## Master Node
  - You can see all line of master node code with `cat ~/_Lecture_k8s_starter.kit/ch1/1.2/k8s-min-5GiB/master_node.sh`
  - In this master node, for example, you can use `k` like `kubectl`

<figure>
  <a href="/assets/img/posts/kubernetes_basic/42.jpg"><img src="/assets/img/posts/kubernetes_basic/42.jpg"></a>
  <figcaption>Alias</figcaption>
</figure>

  - Now, we will use keq as `kubectl exec [Your Pod Number]`.
  - You can see the code with `cat ~/_Lecture_k8s_starter.kit/ch6/6.1/k8s_rc.sh`

<figure>
  <a href="/assets/img/posts/kubernetes_basic/45.jpg"><img src="/assets/img/posts/kubernetes_basic/45.jpg"></a>
  <figcaption>Alias</figcaption>
</figure>

  - To run this file, enter ` ~/_Lecture_k8s_starter.kit/ch6/6.1/k8s_rc.sh`.
  - When you type `keq`, you can see the selection.
  - The default command line environment is shell, but if you want, you can change to bash with `/bin/bash`.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/46.jpg"><img src="/assets/img/posts/kubernetes_basic/46.jpg"></a>
  <figcaption>Alias</figcaption>
</figure>

# Upgrade
  - Before upgrading, our master node version is 1.25.0.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/47.jpg"><img src="/assets/img/posts/kubernetes_basic/47.jpg"></a>
  <figcaption>Upgrade</figcaption>
</figure>

## Master node
  - To upgrade your kubernetes, you should upgrade kubeadm with  `yum upgrade kubeadm-[Your Upgrade Version] -y`, cluster with `kubeadm upgrade apply [Your Upgrade Version] -y`, kubelet with `yum upgrade kubelet-[Your Upgrade Version] -y`.
  - Now, you need to restart kubelet with `systemctl restart kubelet` and `systemctl daemon-reload`.

## Worker node
  - In worker node, you should upgrade kubeadm with `yum upgrade kubeadm-[Your Upgrade Version] -y`, cluster with `kubeadm upgrade node`, kubelet with `yum upgrade kubelet-[Your Upgrade Version] -y`
  - When you have different cluster between master node and worker node, you can use `kubeadm upgrade node` and `kubectl -n kube-system get cm kubeadm-config -o yaml`.
  
<figure>
  <a href="/assets/img/posts/kubernetes_basic/48.jpg"><img src="/assets/img/posts/kubernetes_basic/48.jpg"></a>
  <figcaption>Upgrade</figcaption>
</figure>

  - Now, you need to restart kubelet with `systemctl restart kubelet` and `systemctl daemon-reload`.
  - You can see upgrade result in master node with `kubectl get nodes`.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/49.jpg"><img src="/assets/img/posts/kubernetes_basic/49.jpg"></a>
  <figcaption>Upgrade</figcaption>
</figure>

## Automatic Upgrade with Ansible
  - You can check the installer code for ansible with `cd _Lecture_k8s_starter.kit/ch8/009` and `vi ansible-installer.sh`
  - `yum install ansible-2.9.27-1.el7 -y` will install ansible.
  - `cat <<EOF > /etc/ansible/hosts [Master] 192.168.1.10 [Workers] 192.168.1.[101:103] EOF` describes upgrading hosts.
  - `known_host` makes automatic authorization in ssh without password.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/50.jpg"><img src="/assets/img/posts/kubernetes_basic/50.jpg"></a>
  <figcaption>Upgrade</figcaption>
</figure>

  - You should run this file with `./ansible-installer.sh`,

<figure>
  <a href="/assets/img/posts/kubernetes_basic/51.jpg"><img src="/assets/img/posts/kubernetes_basic/51.jpg"></a>
  <figcaption>Upgrade</figcaption>
</figure>

  - There is an upgrade yaml code and you can check this with `vi k8s-upgrade.yml`.
  - `hosts` means host for upgrading.
  - In task, you can see `yum` and this describes we want to upgrade kubeadm, cluster and kubelet.
  - `ansible.builtin.systemd` shows, we want to restart our kubelet and reload daemon.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/52.jpg"><img src="/assets/img/posts/kubernetes_basic/52.jpg"></a>
  <figcaption>Upgrade</figcaption>
</figure>

  - After you check your upgrade version, run this upgrade code with `ansible-playbook k8s-upgrade.yml`.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/53.jpg"><img src="/assets/img/posts/kubernetes_basic/53.jpg"></a>
  <figcaption>Upgrade</figcaption>
</figure>

# DNS
  - Reference : <a href="https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/">Kubernetes DNS Query</a>
  - Use that manifest to create a Pod with `kubectl apply -f https://k8s.io/examples/admin/dns/dnsutils.yaml`.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/54.jpg"><img src="/assets/img/posts/kubernetes_basic/54.jpg"></a>
  <figcaption>Upgrade</figcaption>
</figure>

  - Take a look inside the resolv.conf file with `kubectl exec -ti dnsutils -- cat /etc/resolv.conf`

<figure>
  <a href="/assets/img/posts/kubernetes_basic/55.jpg"><img src="/assets/img/posts/kubernetes_basic/55.jpg"></a>
  <figcaption>Upgrade</figcaption>
</figure>

  - Use the kubectl get pods command to verify that the DNS pod is running with `kubectl get pods --namespace=kube-system -l k8s-app=kube-dns`.
  - The command line will show pods, that their label is kube-dns and they are belong to namespace kube-system.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/56.jpg"><img src="/assets/img/posts/kubernetes_basic/56.jpg"></a>
  <figcaption>Upgrade</figcaption>
</figure>

  - Use the kubectl logs command to see logs for the DNS containers with `kubectl logs --namespace=kube-system -l k8s-app=kube-dns`.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/57.jpg"><img src="/assets/img/posts/kubernetes_basic/57.jpg"></a>
  <figcaption>Upgrade</figcaption>
</figure>

# etcd
  - In mater node, there is Api server and etcd.
  - etcd communicates with Api server for cluster status.
  - You can install etcd with `~/_Lecture_k8s_starter.kit/ch8/003/install_etcdctl.sh`.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/60.jpg"><img src="/assets/img/posts/kubernetes_basic/60.jpg"></a>
  <figcaption>etcd</figcaption>
</figure>

  - Create deployments with `k apply -f _Lecture_k8s_starter.kit/ch8/003/sysnet4admin.yaml`
  - Sync Api server and etcd with `ETCDCTL_API=3 etcdctl --endpoints=https://[192.168.1.10]:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key get --prefix=true "" > /tmp/prefix`
  - Then you can see two command line, `cat /tmp/prefix | nl | tail` and `cat /tmp/prefix | nl | grep -i 'pod":"sysnet4admin'`

<figure>
  <a href="/assets/img/posts/kubernetes_basic/61.jpg"><img src="/assets/img/posts/kubernetes_basic/61.jpg"></a>
  <figcaption>etcd</figcaption>
</figure>

  - When you scaled deployments, you should sync again.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/62.jpg"><img src="/assets/img/posts/kubernetes_basic/62.jpg"></a>
  <figcaption>etcd</figcaption>
</figure>

  - When you delete deployments, you should sync again, too.

<figure>
  <a href="/assets/img/posts/kubernetes_basic/62.jpg"><img src="/assets/img/posts/kubernetes_basic/62.jpg"></a>
  <figcaption>etcd</figcaption>
</figure>
