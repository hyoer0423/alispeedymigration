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
若失败您也可以通过此[链接](https://moviec.s3.amazonaws.com/eks-spot-fin.mov?AWSAccessKeyId=ASIAS3OHIS6HPDJ5AZMG&Expires=1624613069&x-amz-security-token=IQoJb3JpZ2luX2VjEDUaDmFwLW5vcnRoZWFzdC0xIkcwRQIgAIFIoNZhhADE1suzKHIZYZK%2FjE2Y7OiYjHh8Ret4qiICIQDNnzznpgkMT6Cb8j9%2BMFbbw6tMys7B%2FhcSyHax07BmCyr6Awj%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F8BEAAaDDE5NjM3NTc3OTIxNCIMpPxdltCXVMjrOAMnKs4Dg4jwQL8%2F5%2BJ%2B9RsPy%2BoTv1I9lmzreFGvNUJjb7o5kKcQmtkNsbBECKxYUYbfbE2ElC0kVwWKed9UFuM8A3pfdCe%2BeAu0ndnnjZI0MzC6gA%2B3b6pLLpIC13b7QXHdjR%2FLn3d3FiLMfdleN6jyd1eaxIkjAjHTve5%2FjN%2FjGT9s5OfA9ScRSul7HJTrROcM02m8lV5%2BVko56pNSQMZbA66Ec7NKdG7cQ5m3fbGiKtgaDKVJN042bjfVXAwy4G9amVFvv%2B0GQWiPB0RcqqOd7E1iH8aYETb36fWBz%2Fu8cDgcvcC%2BMN5%2F5qkHFJ3gsdq4xECjrsOh3igrpF0WO51gF6qY%2FyraqbvbeEDucyLvdCRwul8xfJIxsf13BBLz%2BblSttGX4V4oKJlJnTFlw42a3vlBI9M1euudC5TCgiQIewUg2uKSdPsxrdvU6eyYohtozZeUSz%2BphQ8oMR1U4%2FVGw0qe0lUakj7MR7AqWbYfCadrQlPn2xDDZP3DETbSX6TBqXOjNO9n4C8fcFMHs7j0eY0X191e1OrIc%2FCQ26x4ia8%2B7o0fb%2F%2FaUMgTIPFruJ1adQ8ECxDcawAN1FPxFiUv8LUBJUGmmCUVTkvNdzTlg%2FHpMKuy0IYGOpICWKUXBfJEoWW%2FEibv%2BnOFCrdf2uCubm2HxU3AU1EUFJ6xuFk5IbfKVAFInHkqqpptsg5dE%2FTrZ%2F8YWT8PhaUBwcbNhOk11YlxVe7v0dBSZWmXQ9SmsGK85iaSH64AeEqEbjU9jtXeGMzjpZFKaFSZvQSSbyS9a5Lac5X%2FGVuv6R4DETgEDFtGE0Ilx8iY2alKpRfkCbvmh2Ga7x73ptnBNwDzIWUHbfH0LnB6gKOcnZut6O7uS2CEZE%2F0Hj5V2xfcyvwv7Pru0YTHQ7JfjjF6m65X%2FQe5MlGHi6PwN%2FXNuaZc0%2B3pliIrcDGS66d6nQtvyqlz0YOGfKF3u8MfCA4VqyIa6GOn0RTkVDgIRDgwlEWseA%3D%3D&Signature=zenEFaA3DCqWC7TL6litzatdjT8%3D)观看该实验的视频讲解

#### 部署应用跑在Spot集群上
```bash
kubectl apply -f nginx-to-scaleout1.yaml 
```
#### 部署应用，在kube-ops-view观察spot部署

