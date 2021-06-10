---
title: 实验一：利用EKS创建集群
chapter: false
weight: 10
---

{{% notice note %}}
如果你已经对整个实验有所了解，则可以跳过当前部分，而进入下一个步骤。否则，建议请阅读本部分，了解整个实验的目的以及所需要进行的步骤。
{{% /notice  %}}

#### 概述
本节将指导你如何使用EKS在AWS上安装一个Kubernetes集群。

[Amazon EKS](https://aws.amazon.com/eks/)使在AWS上使用Kubernetes部署、管理和扩展容器化应用程序变得容易。

亚马逊EKS为您在多个AWS可用区运行Kubernetes管理基础设施，以消除单点故障。亚马逊EKS经认证符合Kubernetes，因此您可以使用合作伙伴和Kubernetes社区的现有工具和插件。在任何标准Kubernetes环境中运行的应用程序都是完全兼容的，并且可以轻松地迁移到Amazon EKS。


本次实验环节包括：

1.设置Cloud9开发环境 

2.以 Tokyo region为目标站点，用EKS创建一个Kubernetes群集

3.利用eksctl创建EKS集群
