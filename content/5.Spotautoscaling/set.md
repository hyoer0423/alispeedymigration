---
title: "配置和使用 Cluster Autoscaler（CA）"
chapter: False
weight: 53
---
针对 AWS 平台的 CA 可以支持（1）一个 ASG 节点组（2）多个 ASG 节点组（如本实验前面通过 eksctl 工具创建的多个节点组）
从 eksctl 创建的 ASG 组找出对应的 包含竞价实例的 ASG 节点组名字，本实验节点组名
```bash
spotGroupKey=$CLUSTER_NAME"-nodegroup-dev"
spotNGStackNames=$(aws cloudformation describe-stacks | jq -r --arg spotGroupKey "$spotGroupKey" '.Stacks[] | select(.StackName | contains($spotGroupKey)) | .StackName')
echo $spotNGStackNames

i=1
for ng in $spotNGStackNames;  do
    echo $ng;
    export "ASG_SPOT_NAME_"$i=$(aws cloudformation describe-stack-resources --stack-name $ng | jq -r '.StackResources[] | select(.ResourceType=="AWS::AutoScaling::AutoScalingGroup") | .PhysicalResourceId')
    i=$((i+1));
done

echo $ASG_SPOT_NAME_1
echo $ASG_SPOT_NAME_2
```
显示如下：
![](/images/ACKToEKS/echo.png)
#### 查看是否设置ROLE_NAME
```bash
echo =$ROLE_NAME
```
#### 应用 CA 所需的相应权限
```bash
aws iam put-role-policy --role-name $ROLE_NAME --policy-name ASG-Policy-For-Worker --policy-document file://k8s-asg-policy.json --region ${AWS_REGION}

aws iam get-role-policy --role-name $ROLE_NAME --policy-name ASG-Policy-For-Worker --region ${AWS_REGION}
```
显示如下：
![](/images/ACKToEKS/policy.png)
#### 部署CA
```bash
kubectl apply -f cluster_autoscaler1.yml
```
#### 扩展服务，观察kube-ops-view
```bash
kubectl scale --replicas=10 deployment/nginx-to-scaleout
```
#### 驱逐Node,观察kube-ops-view
```bash
kubectl get nodes
kubectl cordon #xxxxnode
kubectl drain #nodename —-ignore-daemonsets
```

