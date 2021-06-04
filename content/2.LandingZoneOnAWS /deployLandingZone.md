---
title: "创建Cloudformation模版"
chapter: false
weight: 22
---

## 通过Cloudformation模版部署
* 进入CloudFormation平台：[Tokyo CloudFormation](https://ap-northeast-1.console.aws.amazon.com/cloudformation/home?region=ap-northeast-1#/)
* 可以修改REGION变量进入任意地区https://REGION.console.aws.amazon.com/cloudformation/home?region=REGION#

### 创建stack
将此 url(**https://s3.amazonaws.com/aws-kubernetes-artifacts/v0.5/lab-ide-vpc.template**
) 填入 Amazon S3 URL 
![](/images/ACKToEKS/stack.png)
接受默认的堆栈名称，然后点击 **NEXT**。你可以给**Tag**，如Key=Name，Value=k8s-workshop，然后点击**NEXT**。确保勾选 **I acknowledge that AWS CloudFormation might create IAM resources with custom names**，然后点击**create**。

CloudFormation会创建嵌套堆栈，并建立该研讨会所需的几个资源。等到所有的资源都创建完毕。一旦k8s-workshop的状态变为CREATE_COMPLETE，你就可以打开Cloud9 IDE。要打开Cloud9 IDE环境，点击CloudFormation Console中的 "Outputs "标签，并点击 "Cloud9IDE "URL。
![](/images/ACKToEKS/output.png)

