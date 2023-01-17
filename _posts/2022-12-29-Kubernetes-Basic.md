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

  - By the way, to restart our kubelet and scheduler, enter `systemctl start containerd`

<figure>
  <a href="/assets/img/posts/kubernetes_basic/37.jpg"><img src="/assets/img/posts/kubernetes_basic/37.jpg"></a>
  <figcaption>Issues</figcaption>
</figure>


