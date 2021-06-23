---
title: "开始准备"
chapter: False
weight: 51
---
#### 查看环境变量
```bash
echo "export CLUSTER_NAME=eksworkshop-eksctl" | tee -a ~/.bash_profile
echo "export AWS_REGION=${AWS_REGION}" | tee -a ~/.bash_profile
```
{{% notice note %}}
如果**CLUSTER_NAME** 变量为空，请通过以下命令设置：
{{% /notice  %}}
1.查看cluster名字
```bash
 aws eks list-clusters
```
```bash
{
    "clusters": [
        "eksworkshop-eksctl"
    ]
}
```

#### 通过以下下命令添加role
```bash
source ~/.bash_profile

STACK_NAME=$(eksctl get nodegroup --cluster $CLUSTER_NAME -o json | jq -r '.[].StackName')
ROLE_NAME=$(aws cloudformation describe-stack-resources --stack-name $STACK_NAME | jq -r '.StackResources[] | select(.ResourceType=="AWS::IAM::Role") | .PhysicalResourceId')
echo "export ROLE_NAME=${ROLE_NAME}" | tee -a ~/.bash_profiles
```
#### 增加新的Nodegroup
查看nodegroup 情况
```bash
eksctl get nodegroup --cluster ${CLUSTER_NAME}
```
nodegroup 扩容
注意<desiredCount> ,<minSize> ,<maxSize>为变量，可以自己设置大小，如3，1，2
```bash
eksctl scale nodegroup --cluster=${CLUSTER_NAME} --nodes=<desiredCount> --name=nodegroup --nodes-min=<minSize>  --nodes-max=<maxSize> 
eksctl get nodegroup --cluster ${CLUSTER_NAME}
```
查看node labels
```bash
kubectl get nodes --show-labels
```

旧有的nodes 添加label lifecycle=OnDemand
```bash
kubectl label nodes --all lifecycle=OnDemand
kubectl get nodes -L lifecycle
```
显示如下：
![](/images/ACKToEKS/lifecycle.png)
## 下载环境
```bash
curl -O https://shawnyj.s3.ap-northeast-1.amazonaws.com/immersiondays.zip
unzip immersiondays.zip
cd immersiondays
```