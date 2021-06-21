---
title: "利用eksctl来创建结点"
chapter: false
weight: 24
---

eksctl是一个由AWS和Weaveworks联合开发的工具，它将创建EKS集群的大部分经验自动化。

在这个模块中，我们将回到cloud9使用eksctl来启动和配置我们的EKS集群和节点。

对于这个模块，我们需要下载eksctl二进制文件。
```bash
curl --silent --location "https://github.com/weaveworks/eksctl/releases/download/0.44.0/eksctl_Linux_amd64.tar.gz" | tar xz -C /tmp

sudo mv -v /tmp/eksctl /usr/local/bin
```
启用eksctl bash-completion
```bash
eksctl completion bash >> ~/.bash_completion
. /etc/profile.d/bash_completion.sh
. ~/.bash_completion
```
创建集群
{{% notice note %}}
若创建集群出现 Error: field secretsEncryption.keyARN is required for enabling secrets encryption，表明您尚未创建 KMS 密钥，则可以使用 AWS CLI 创建密钥和别名
{{% /notice  %}}
```
$ MASTER_ARN=$(aws kms create-key --query KeyMetadata.Arn --output text)
$ aws kms create-alias \
      --alias-name alias/k8s-master-key \
      --target-key-id $(echo $MASTER_ARN | cut -d "/" -f 2)
```
创建一个eksctl部署文件（eksworkshop.yaml），用于创建你的集群，使用以下命令：
```bash
cat << EOF > eksworkshop.yaml
---
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: eksworkshop-eksctl
  region: ${AWS_REGION}
  version: "1.17"

availabilityZones: ["${AZS[0]}", "${AZS[1]}", "${AZS[2]}"]

managedNodeGroups:
- name: nodegroup
  desiredCapacity: 3
  instanceType: t3.small
  ssh:
    enableSsm: true

# To enable all of the control plane logs, uncomment below:
# cloudWatch:
#  clusterLogging:
#    enableTypes: ["*"]

secretsEncryption:
  keyARN: ${MASTER_ARN}
EOF
```

接下来，使用你创建的文件作为eksctl集群创建：
```bash
eksctl create cluster -f eksworkshop.yaml
```