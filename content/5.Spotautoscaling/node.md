---
title: "部署竞价实例"
chapter: False
weight: 52
---
#### 创建单竞价实例工作组
利用 EKSCTL 工具创建两个 SPOT ASG 工作节点组，AutoScaler 要求每个组的实例类型拥有一致的 vCPU 和 Memory 的值，因此为了实例多样性，我们实验中使用了 2 个 ASG 组来模拟更多的实例类型
```bash
envsubst < ./eks-node-groups.yml.template >eks-node-groups.yml
eksctl create nodegroup -f ./eks-node-groups.yml
kubectl get nodes -L lifecycle
```
显示如下：
![](/images/ACKToEKS/spot.png)
#### 部署竞价实例中断监听和处理程序（DaemonSet Pod）
该中断信号监听和处理程序包含以下功能逻辑：
* 监听竞价实例被中断通知
* 利用 2分钟中断处理预留时间窗口，准备好该节点被终止处理
  *  Taint & cordon 该节点，使得新的 Pod 不会在该节点上启动
  *  对于该节点上的 Pod 已有链接，等待链接耗尽
  * 在其他节点上重启该节点上受影响的 Pods 以便于保障服务的容量 
```bash
kubectl apply -f https://github.com/aws/aws-node-termination-handler/releases/download/v1.3.1/all-resources.yaml
kubectl get daemonsets -n kube-system
```
显示如下：
![](/images/ACKToEKS/get_dea.png)
安装webhook：
```bash
kubectl apply -f https://raw.githubusercontent.com/nwcdlabs/container-mirror/master/webhook/mutating-webhook.yaml
```

#### 安装 kube-ops-view 方便观察节点资源和相应的 Pod 情况
首先安装[helm](https://www.eksworkshop.com/beginner/060_helm/helm_intro/install/index.html)：

```bash
curl -sSL https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
helm repo add stable https://charts.helm.sh/stable
helm search repo stable
helm completion bash >> ~/.bash_completion
. /etc/profile.d/bash_completion.sh
. ~/.bash_completion
source <(helm completion bash)
```
利用helm 安装 kube-ops-view
```bash
helm install kube-ops-view \
stable/kube-ops-view \
--set service.type=LoadBalancer \
--set rbac.create=True
```
部署kube-ops-view
```bash
kubectl apply -f kube-ops-view/deploy
kubectl describe svc kube-ops-view 
kubectl get svc kube-ops-view | tail -n 1 | awk '{ print "Kube-ops-view URL = http://"$4 }'
```
得到的域名使用：808端口登陆，例如：
http://a13ac3d385cba4bddb7d91e97c0e1f95-923967614.cn-northwest-1.elb.amazonaws.com.cn:808
#### 部署应用跑在Spot集群上
```bash
kubectl apply -f nginx-to-scaleout1.yaml 
```
#### 部署应用，在kube-ops-view观察spot部署

