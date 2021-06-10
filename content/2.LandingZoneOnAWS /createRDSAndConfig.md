---
title: "创建主节点"
chapter: false
weight: 34
---

## 创建主节点

1.使用以下命令创建一个Kubernetes集群。在Cloud9 IDE底部的 "bash "终端标签中运行它。这将创建一个带有主节点的集群:
```bash
$ aws eks create-cluster \
  --name k8s-workshop \
  --role-arn $EKS_SERVICE_ROLE \
  --resources-vpc-config subnetIds=${EKS_SUBNET_IDS},securityGroupIds=${EKS_SECURITY_GROUPS} \
  --kubernetes-version 1.20
```
`EKS_SERVICE_ROLE`、`EKS_SUBNET_IDS`和`EKS_SECURITY_GROUPS`环境变量应该已经在[Cloud9环境设置](http://localhost:1313/2.landingzoneonaws/)中设置。
{{% notice note %}}
若显示**ResourceInUseException**问题，请先用以下命令删除cluster
{{% /notice  %}}
```bash
aws eks delete-cluster --name <my-cluster>
```
2.集群配置通常需要不到10分钟。你可以用以下命令查询你的集群的状态。当你的集群状态是**ACTIVE**时，你可以继续。
```bash
$ aws eks describe-cluster --name k8s-workshop --query cluster.status --output text
```
{{% notice note %}}
如果你的 "创建集群 "失败了，出现了如下错误:
```bash
aws: error: argument --role-arn: expected one argument
```
{{% /notice  %}}
在执行 "创建集群 "命令之前，请确认以下环境变量已经设置。
```bash
echo $EKS_SERVICE_ROLE
echo $EKS_SUBNET_IDS
echo $EKS_SECURITY_GROUPS
```
如果这些环境变量中的任何一个是空白的，请重新运行[Cloud9环境设置](http://localhost:1313/2.landingzoneonaws/)。




