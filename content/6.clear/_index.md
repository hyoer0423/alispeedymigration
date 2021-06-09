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

#od_nodegroup=$(eksctl get nodegroup --cluster $EKS_CLUSTER_NAME | tail -n 1 | awk '{print $2}')
#eksctl delete nodegroup --cluster $EKS_CLUSTER_NAME --name $od_nodegroup
eksctl delete cluster --name $CLUSTER_NAME
```