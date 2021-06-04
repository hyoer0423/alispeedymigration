---
title: "Cloud9 设置"
chapter: false
weight: 23
---
### Cloud9 实例设置

Cloud9 IDE需要使用指定的IAM Instance配置文件。打开 "AWS Cloud9 "菜单，进入 "首选项"，再进入 "AWS设置"，并禁用 "AWS管理的临时凭证"，如图中所示。

![](/images/ACKToEKS/c9.png)
### 准备Cloud9
一旦你的Cloud9准备好了，请下载构建脚本并在你的IDE中安装。这将使你的IDE为运行本工作坊的教程做好准备。该构建脚本会安装以下内容。
* jq
* kubectl
* heptio/authenticator（用于对EKS集群的认证,更新/配置AWS CLI并在bash_profile中存储必要的环境变量)
* kops
* 创建一个SSH密钥
* 克隆 the workshop repository 到 Cloud9
要安装该脚本，请在Cloud9 IDE的 "bash "终端标签中运行该命令：
```bash
aws s3 cp s3://aws-kubernetes-artifacts/v0.5/lab-ide-build.sh . && \
chmod +x lab-ide-build.sh && \
. ./lab-ide-build.sh
```
此时，你可以重启Cloud9 IDE的终端会话，以确保kubectl的完成度被启用。一旦打开一个新的终端窗口，输入kubectl ver，按Tab键自动完成，然后按Enter键。这将确保kubectl工具正确安装在命令行上，并能自动完成。
