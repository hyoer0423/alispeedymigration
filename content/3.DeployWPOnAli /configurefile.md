---
title: "创建worker nodes"
chapter: false
weight: 33
---
## 创建worker nodes
现在你的EKS主节点已经创建，你可以启动和配置你的工作节点。为了启动你的worker node，运行以下CloudFormation CLI命令:
```bash
$ aws cloudformation create-stack \
  --stack-name k8s-workshop-worker-nodes \
  --template-url https://amazon-eks.s3-us-west-2.amazonaws.com/1.10.3/2018-06-05/amazon-eks-nodegroup.yaml \
  --capabilities "CAPABILITY_IAM" \
  --parameters "[{\"ParameterKey\": \"KeyName\", \"ParameterValue\": \"${AWS_STACK_NAME}\"},
                 {\"ParameterKey\": \"NodeImageId\", \"ParameterValue\": \"${EKS_WORKER_AMI}\"},
                 {\"ParameterKey\": \"ClusterName\", \"ParameterValue\": \"k8s-workshop\"},
                 {\"ParameterKey\": \"NodeGroupName\", \"ParameterValue\": \"k8s-workshop-nodegroup\"},
                 {\"ParameterKey\": \"ClusterControlPlaneSecurityGroup\", \"ParameterValue\": \"${EKS_SECURITY_GROUPS}\"},
                 {\"ParameterKey\": \"VpcId\", \"ParameterValue\": \"${EKS_VPC_ID}\"},
                 {\"ParameterKey\": \"Subnets\", \"ParameterValue\": \"${EKS_SUBNET_IDS}\"}]"
```
{{% notice note %}}
`AWS_STACK_NAME`、`EKS_WORKER_AMI`、`EKS_VPC_ID`、`EKS_SUBNET_IDS`和`EKS_SECURITY_GROUPS`环境变量已经在[Cloud9环境设置](http://localhost:1313/2.landingzoneonaws/)中设置。

{{% /notice  %}}
节点配置通常需要不到5分钟。你可以通过以下命令查询你的集群的状态:
```bash
$ aws cloudformation describe-stacks --stack-name k8s-workshop-worker-nodes --query 'Stacks[0].StackStatus' --output text
```
当你的集群状态为**CREATE_COMPLETE**时，你可以继续。