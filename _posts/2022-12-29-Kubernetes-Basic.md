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

  