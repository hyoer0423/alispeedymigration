---
title: "创建配置文件"
chapter: false
weight: 32
---

## 创建配置文件

为了在本地访问集群，使用一个配置文件（有时被称为kubeconfig文件,这个配置文件可以自动创建。
一旦集群移动到**ACTIVE**状态，下载并运行create-kubeconfig.sh脚本。
```bash
aws s3 cp s3://aws-kubernetes-artifacts/v0.5/create-kubeconfig.sh . && \
chmod +x create-kubeconfig.sh && \
. ./create-kubeconfig.sh
```

这将在$HOME/.kube/config创建一个配置文件，并为默认访问更新必要的环境变量。你可以使用 "kubectl get service "测试你的kubectl配置

```bash
$ kubectl get service
```
运行完上面命令后，出现如下：
```bash
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.100.0.1   <none>        443/TCP   6m
```
