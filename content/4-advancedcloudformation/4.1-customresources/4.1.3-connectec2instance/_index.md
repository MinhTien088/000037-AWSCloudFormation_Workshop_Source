---
title : "Connect EC2 Instance"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 4.1.3. </b> "
---

#### Connecting EC2 Instance

1. In the [AWS Management Console](https://console.aws.amazon.com/console/)

- Find **Systems Manager**
- Select **Systems Manager**

![Connect to EC2 instance](/images/4.advancedcloudformation/0001-connectinstance.png?width=5120px)

2. In the **AWS Systems Manager** interface

- Select **Parameter Store**

![Connect to EC2 instance](/images/4.advancedcloudformation/0002-connectinstance.png?width=5120px)

3. In the **Parameters Store** interface

- Select **My parameters**
- Select **MyKey01**

![Connect to EC2 instance](/images/4.advancedcloudformation/0003-connectinstance.png?width=5120px)

4. In the **MyKey01** interface

- Select **Overview**
- See **Last modified user**
- Select **Show** Value

![Connect to EC2 instance](/images/4.advancedcloudformation/0004-connectinstance.png?width=5120px)

5. Copy the value and save **cloudformation.pem**

![Connect to EC2 instance](/images/4.advancedcloudformation/0005-connectinstance.png?width=5120px)

6. Use PuTTY Key Generator to load key and **Save private key**

![Connect to EC2 instance](/images/4.advancedcloudformation/0006-connectinstance.png?width=5120px)

7. Use **PuTTY** to connect **EC2 Instance**

![Connect to EC2 instance](/images/4.advancedcloudformation/0007-connectinstance.png?width=5120px)

8. When connecting enter user name:**ec2-user**

![Connect to EC2 instance](/images/4.advancedcloudformation/0008-connectinstance.png?width=5120px)

9. Use **ifconfig -a** command to display information of all network interfaces

![Connect to EC2 instance](/images/4.advancedcloudformation/0009-connectinstance.png?width=5120px)

10. Do **ping amazon.com -c5** to test the connection

![Connect to EC2 instance](/images/4.advancedcloudformation/00010-connectinstance.png?width=5120px)
