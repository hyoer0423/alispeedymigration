---
title: "获取IAM权限"
chapter: false
weight: 41
---

### 获取IAM权限
1.创建一个具有管理员权限的IAM角色，[点击此](https://console.aws.amazon.com/iam/home#/roles$new?step=review&commonUseCase=EC2%2BEC2&selectedUseCase=EC2&policies=arn:aws:iam::aws:policy%2FAdministratorAccess&roleName=eksworkshop-admin)。    
2.确认选择了AWS服务和EC2，然后点击下一步。权限，查看权限。    
3.确认AdministratorAccess被选中，然后点击下一步。标签来分配标签。
![](/images/ACKToEKS/IAM.png)     
4.采用默认值，然后点击下一步。审查来审查。     
5.在名称中输入eksworkshop-admin，然后点击创建角色。     
### 将IAM ROLE 添加到CLoud 9 workspace中
1.回到[cloud9平台](https://console.aws.amazon.com/cloud9/home/account)，创建完environemnt后，点击灰色圆圈按钮（在右上角），选择**Manage EC2 Instance**  

2.再次选择**Actions / Security / Modify IAM Role**     

3.从IAM ROLE的下拉菜单中选择eksworkshop-admin，并选择保存。  

### 更新IAM设置
为了确保临时凭证尚未到位，我们还将删除任何现有的凭证文件。
```bash
rm -vf ${HOME}/.aws/credentials
```
### 出现 IAM role valid 可以进行下一步