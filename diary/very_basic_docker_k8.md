% Very basic Docker and Kubernetes
% By Lars Christoffersen
% Date 02/11/2020

---
layout: page
title: Very Basic Docker and K8 course
permalink: /diary/terminal
--- 

# What is this about

- A little bit about docker
- Some stuff about kubernetes and how we use it
- In the end both you and I will hopefully be a little wiser

# Where do we use Kubernetes in the ØBS

- We have 3 clusters.
- One for dev and test
- Two for production, but only one is active
- In them we are running services for transformations
- And services for API, Calculation Engine and Dashboard
- Deployment is automated (more or less)


# Docker!

![Deployment types through time](https://d33wubrfki0l68.cloudfront.net/26a177ede4d7b032362289c6fccd448fc4a91174/eb693/images/docs/container_evolution.svg)
- Most popular containerisation tool out there. There are a few others i.e. Open shift from refhat and Mesos from Apache.
- **Docker IS NOT A VIRTUAL MACHINE** 
- DEMO: Run a docker container from a Dockerfile on the local machine and show the MSQL Database
- [Docs] if you really want to see how it is working under the hood, watch [Liz Rice video on building a container from scratch in Go](https://youtu.be/8fi7uSYlOdc) or se [her github page](https://github.com/lizrice/containers-from-scratch) and do the experiments


# Kubernetes

![image of deployments](https://d33wubrfki0l68.cloudfront.net/2475489eaf20163ec0f54ddc1d92aa8d4c87c96b/e7c81/images/docs/components-of-kubernetes.svg)
- Service discovery and load balancing
- Storage orchestration
- Automated rollouts and rollbacks
- Automatic bin packing
- Self-healing
- Secret and configuration management
- Architecture: There is a fine [explanation in the docs](https://kubernetes.io/docs/concepts/overview/components/)
- [Docs](https://kubernetes.io/docs/concepts/overview/components/)


# Kubernetes Pods

- Pods are the smallest deployable units of computing that you can create and manage in Kubernetes.
- Pods that run a single container
- Pods that run multiple containers that need to work together
- Demo: Lets make a pod out of the flask container mock up thingy   
- [Docs](https://kubernetes.io/docs/concepts/overview/components/)

# Kubernetes API or Cli

- Is available through the kubectl cli which
- The way to interact with Kubernetes
- [docs](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)







