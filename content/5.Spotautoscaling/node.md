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
若失败您也可以通过此[链接](https://moviec.s3.amazonaws.com/eks-spot-fin.mov?AWSAccessKeyId=ASIAS3OHIS6HFAXQZ6HD&Expires=1624618182&x-amz-security-token=IQoJb3JpZ2luX2VjEDcaDmFwLW5vcnRoZWFzdC0xIkYwRAIgLbmxv1aoMbn2omJf%2F9yTywdS6OuExPWu93rvz03LnWwCIB348U4ITOv6DKl2KD4eHNto6JGbN0l0AAfWPm2U%2F0m0KtsDCBAQABoMMTk2Mzc1Nzc5MjE0IgxkEbK9zZ%2FTxCICpaoquANDe5iXH6CcDxb3nSdk6YelxE3%2B65%2FIYjPgjx9YN4MK5wpWFrKA5TD4YL0REaU453Qw2WAClwka%2FAVlW4nEecaxrJIvEcPOiSG0UxqNQ9UQvvAYJ7OZuuD0427WndB7kiCV%2FOTDYF21BbRM7FMqr9DJrQIAhxcjfZuwup6aFY9uShtWrqNU2SPr938djn3O05JKgBVKGOHIWUr%2FkvDY5ADVHJ7dCEs1YhBBlP6D%2BVuC%2B%2BXIMWGDc3X%2BYuJPYn28Db8DDz0ATS0lDwP8VySj%2FlTwK7GS21I4IFdW%2BowpfZYEsyomAUnKyNxX0n81we3%2FyHjTqEaaEhjOFTMPFzBgfNP982SD2eDnqdf%2BDZOJJT9oL9k%2FCstCo2ShY5BtqISZRfJNu5byGCjZarsDkFuzxPXYnLd5Jw0uzRFI8TLawhOKOcyqD2d%2FHj%2B%2FU1oIpu2CVIwp53myr3w207SlKgPs7d%2Bq5zk7M3gpBpjIcuE%2F%2B3wqh1xHsdISYKuJOJc1o5OpnwJ%2F1seeEaSUaAXnpRmhp%2FZEBqpTrEbsgJ8fEXBXEG%2F2181j%2FjVUJkb6k8wvUe2HtJPX7dyOcjseADDC2tCGBjqTAsUqRlAskgTGEvnj7Yc0FjMLY6ZU3B58OJgKmJCfA%2BSoONCdk4gz64l3iFus9j2UgQvADwQaHS9U2HZ8G2xJLPJIaUs0O%2B2FQNjblXIiWB5R7fHacMoyd%2FnZeLi6AOglH6ZHiZCbtewHB2Nr78g1JDwRHG4An6L%2FpVnD7APwepFfobAmMi0N1TP%2BSdBUZVYpTkotZgW%2BcWMAT0LG4tSIOyOYpNK4Hf%2FqyqceAtZOUb3yZXhS6VlyHTP00bwfbzV6QDr8kTH5pE3IDYsVJlxEhQC5%2BuCJje2EUpWSoH0drmFTTlhrjIWNNJoP9eKYN6jNmq5dI9wV1z44uhe57EDjoLs9NkNmPxPjFptNrA5j8m40mE%2FN&Signature=6n%2Bu8RUrO8XlVncFsyoTZ54av%2B0%3D)观看该实验的视频讲解

#### 部署应用跑在Spot集群上
```bash
kubectl apply -f nginx-to-scaleout1.yaml 
```
#### 部署应用，在kube-ops-view观察spot部署

