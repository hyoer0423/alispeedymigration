---
title: "获取IAM权限"
chapter: false
weight: 22
---

### 获取IAM权限
1.创建一个具有管理员权限的IAM角色，[点击此](https://console.aws.amazon.com/iam/home#/roles$new?step=review&commonUseCase=EC2%2BEC2&selectedUseCase=EC2&policies=arn:aws:iam::aws:policy%2FAdministratorAccess&roleName=eksworkshop-admin)。    
2.确认选择了**AWS Service**和**EC2**，然后点击下一步：**Permissions**。    
3.确认AdministratorAccess被选中，然后点击下一步：**Tag**来分配标签。
![](/images/ACKToEKS/IAM.png)     
4.采用默认值，然后点击下一步：**Review**来审查。     
5.在名称中输入eksworkshop-admin，然后点击**Create role**。
![](/images/ACKToEKS/Role.png)       
### 将IAM ROLE 添加到Cloud 9 workspace中
1.回到[cloud9平台](https://console.aws.amazon.com/cloud9/home/account)，创建完environemnt后，点击灰色圆圈按钮（在右上角），选择**Manage EC2 Instance**  
![](/images/ACKToEKS/set.png)  
2.再次选择**Actions / Security / Modify IAM Role**  
![](/images/ACKToEKS/modify.png) 
3.从IAM ROLE的下拉菜单中选择eksworkshop-admin，并选择保存。  
![](/images/ACKToEKS/choose.png) 
### 关闭Cloud9 的AWS managed temporary credentials设置
* 回到cloud9平台，选择设置
* 选择**AWS SETTINGS**
* 关闭 **AWS managed temporary credentials**
* 关掉设置窗口
![](/images/ACKToEKS/off_set.png) 
### 更新IAM设置
为了确保临时凭证尚未到位，我们还将删除任何现有的凭证文件。
```bash
rm -vf ${HOME}/.aws/credentials
```
