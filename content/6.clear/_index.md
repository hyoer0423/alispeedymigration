---
title: "清除实验资源"
chapter: false
weight: 60
---
#### 清除实验资源
首先, 清除 EKS 服务:
```bash
kubectl delete -f ./nginx-to-scaleout1.yaml
kubectl delete -f ./cluster_autoscaler1.yml
kubectl delete -f ./kube-ops-view/deploy
```
其次，关闭 EKS 节点组
```bash
eksctl delete nodegroup -f eks-node-groups.yml --approve

eksctl delete cluster --name $CLUSTER_NAME
```
#### 也可以点击此[链接](https://console.aws.amazon.com/eks/home#/clusters)手动删除生成的cluster