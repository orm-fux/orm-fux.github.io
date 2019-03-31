---
layout: post
title:  Kubernetes/Minikube Quick Set Up Commands
date:   2019-03-03 07:45:00
tags: cheatsheet kubernetes minikube
short_description: Concise listing of command to install and set up Kubernetes/Minikube for local development
mathjax: false
---

This is just a simple and quick listing of commands for Ubuntu systems to install Kubernetes (`kubectl`) and Minikube on a local machine. Minikube is running with VirtualBox.

```shell
$ sudo apt install virtualbox
$ sudo apt install minikube
$ sudo snap install kubectl --classic
$ cd ~/bin \
	&& curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 \
	&& chmod +x minikube
$ minikube start 
$ cd .kube \
	&& kubectl completion bash > bash_completion
	&& chmod a+x bash_completion \
	&& echo "source ~/.kube/bash_completion" > .bash_aliases
```

Links for further information:
* [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
* [Install Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)
* [Install and Set Up Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
