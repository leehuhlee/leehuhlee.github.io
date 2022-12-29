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
