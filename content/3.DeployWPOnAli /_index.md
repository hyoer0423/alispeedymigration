---
title: "用EKS创建一个Kubernetes集群"
chapter: false
weight: 30
---

## 用EKS创建一个Kubernetes集群

亚马逊EKS为您在多个AWS可用区运行Kubernetes管理基础设施，以消除单点故障。亚马逊EKS经认证符合Kubernetes，因此您可以使用合作伙伴和Kubernetes社区的现有工具和插件。在任何标准Kubernetes环境下运行的应用程序都是完全兼容的，可以很容易地迁移到亚马逊EKS。
{{% notice note %}}
如果你还没有设置Cloud9开发环境，请在继续之前设置好。
{{% /notice  %}}

## Permission

当通过Cloud9 IDE部署Cloud9环境和运行构建脚本时，会自动创建权限。

由Cloud9 IDE承担的IAM角色将有一个类似于k8s-workshop-LabIdeRole-xxxx的名字，并包含以下管理策略。

* AmazonEC2FullAccess

* IAMFullAccess

* AmazonS3FullAccess

* AmazonVPCFullAccess

* AWSCloudFormationReadOnlyAccess

* AmazonRoute53FullAccess

EKS服务角色的名称将类似于k8s-workshop2-EksServiceR-AWSServiceRoleForAmazonE-xxx，并包含以下管理策略。

* AmazonEKSClusterPolicy

* AmazonEKSServicePolicy


