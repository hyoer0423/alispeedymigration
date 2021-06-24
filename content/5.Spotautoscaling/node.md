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
{{% notice info %}}
请注意您可以使用下方命令来查看部署情况，若Ready显示数为**0/1**，您需要删除并重新部署
同时请保证您并没有链接代理和vpn，否则该端口将无法被访问
{{% /notice  %}}
```bash
kubectl get deployments
```
#### 删除并重新部署命令如下：
```bash
kubectl delete deployment --all 
kubectl delete service kube-ops-view
kubectl delete service kube-ops-view-redis
kubectl apply -f kube-ops-view/deploy
kubectl describe svc kube-ops-view 
kubectl get svc kube-ops-view | tail -n 1 | awk '{ print "Kube-ops-view URL = http://"$4 }'
```
若失败您也可以通过此[链接](https://moviec.s3.amazonaws.com/eks-spot-fin.mov?AWSAccessKeyId=ASIAS3OHIS6HFJ43B2MN&Expires=1624512189&x-amz-security-token=IQoJb3JpZ2luX2VjEDIaDmFwLW5vcnRoZWFzdC0xIkYwRAIgFCAlOTAJoNIP2dhmFg6ewfcI51gPqla5vDN8NKknt58CIAmzTENxqnAGwptWj%2BvhotlTatdF1PQsTX4ojU5dLVGIKvwDCPz%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEQABoMMTk2Mzc1Nzc5MjE0IgzrkEH2baaqEgVqJygq0AMHOV6kJnNK6%2Bf%2BnUosQJrQv7WTuTnh6jOSaR3f%2F6P2Rg0FSRdxZYqcglAItlKyGGdzxoByz56j2idCbD9f7B7dBOBLMx%2Bm%2BqMK4t8qAx3vFceVhrJwzSg2tHPe7VpfQ9OqMjLbQt%2B9oqzlLi%2B4oOap1sPZcPAJUdNRlXrf%2B5Ld7UfwPb4Qmxp7W7i0ttZVU4wzIY0FprxJ6liwgi7NBQqtmIrfMvrDkWct9fNiX22NfusRLQq22%2B6jfw%2FC3rw4IfMS3jJX0YsEZll2asKwm45qlF4iVaRNefmQXCdjREYOBEvSXfZLV1pBdBthJufjNIMJ2nZXT0ukkJ5b%2BxJ8K%2F8ycuLGTDpIVX3IWSMHha4z%2BJCP1g%2FnjMmP99IstJ0Zfh%2B5RmTLyd%2Fqplvcz4NHdvRXFmG7rxNMVP6eOk2%2Bw%2FZs4obys7t5CGBhBwE0b3cmvseIu7YA%2Fudbv0pv1H6rUtECgJXY3s5%2BbWnCl2JR3SOrLbMehXzJw6MWf8Nzt9boks0BEDjRZPQMUrqBrLnznYLBsg%2FVvPfIRQcuzutY1ontmxYIuP73hEDDCA3ECAK0R76eEZ4rprvJEqSqrhzbuxfugc31RjtJ7W%2FYyb4JC%2B8sXzCfqs2GBjqTAiRzKOGAVgJpZu9wCQCzRt2jItfRq7Ysi%2BnMPnDjFMqn9YHUMjvWu4l6BQ1oNow48c1xIN4TU%2BUT03kCc3yGUggRZFnCPvl2XqfwKZYvUrqfIagz6WEdlqlKpErbLK6qWNkjtQ1Niy9DXZp7lndKm4LPKQOuEMHjBsIHsaLMITdQOJtURUuAvni6SBz1FgUFbS%2FP6FcokcN95UCda6siuMI30SsYtPeveKPu6XQe8%2B8a1x69pi94bRDugAZZyWP5XkKQA75jONHrcx0X4jXD1v2eQzNjR4KhsV2Dsz6bll8Dn8%2Bz7tH2Wt43BqOLVx8Q7pBQe1%2FOSzIRi1DGceZTtOLXxfHKf%2FzRR6JuPLZUzS0Nq951&Signature=kfmqjNF%2BBF3GD0EnuHoBKe2zczg%3D)观看该实验的视频讲解

#### 部署应用跑在Spot集群上
```bash
kubectl apply -f nginx-to-scaleout1.yaml 
```
#### 部署应用，在kube-ops-view观察spot部署

