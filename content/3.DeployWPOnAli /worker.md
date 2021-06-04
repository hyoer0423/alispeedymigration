---
title: "worker node加入集群"
chapter: false
weight: 34
---
## worker node加入集群

要使工作节点加入你的集群，请下载并运行**aws-auth-cm.sh**脚本。
```bash
$ aws s3 cp s3://aws-kubernetes-artifacts/v0.5/aws-auth-cm.sh . && \
chmod +x aws-auth-cm.sh && \
. ./aws-auth-cm.sh
```
观察你的节点的状态，等待它们达到**READY**状态。

```bash
$ kubectl get nodes --watch
```