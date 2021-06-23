---
title: 实验一：利用EKS创建集群
chapter: false
weight: 20
---
#### 概述
本节将指导你如何使用EKS在AWS上安装一个Kubernetes集群。eksctl是一个由AWS和Weaveworks联合开发的工具，是一种用于在 Amazon EKS 上创建和管理 Kubernetes 集群的简单命令行实用程序,它将创建EKS集群的大部分经验自动化。在这个模块中，我们将使用eksctl来启动和配置我们的EKS集群和节点。

[Amazon EKS](https://aws.amazon.com/eks/)使在AWS上使用Kubernetes部署、管理和扩展容器化应用程序变得容易。

亚马逊EKS为您在多个AWS可用区运行Kubernetes管理基础设施，以消除单点故障。亚马逊EKS经认证符合Kubernetes，因此您可以使用合作伙伴和Kubernetes社区的现有工具和插件。在任何标准Kubernetes环境中运行的应用程序都是完全兼容的，并且可以轻松地将应用迁移到Amazon EKS。


本次实验环节包括：

1.设置Cloud9开发环境 

2.利用eksctl创建EKS集群

在本教程结束时，您将获得一个可在其中部署应用程序的正在运行的 Amazon EKS 集群。 