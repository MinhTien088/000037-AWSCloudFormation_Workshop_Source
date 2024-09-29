---
title : "Kết nối EC2 Instance"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 4.1.3. </b> "
---

#### Kết nối EC2 Instance

1. Trong giao diện [AWS Management Console](https://console.aws.amazon.com/console/)

- Tìm **Systems Manager**
- Chọn **Systems Manager**

![Connect to EC2 instance](/images/4.advancedcloudformation/0001-connectinstance.png?width=5120px)

2. Trong giao diện **AWS Systems Manager**

- Chọn **Parameter Store**

![Connect to EC2 instance](/images/4.advancedcloudformation/0002-connectinstance.png?width=5120px)

3. Trong giao diện **Parameters Store**

- Chọn **My parameters**
- Chọn **MyKey01**

![Connect to EC2 instance](/images/4.advancedcloudformation/0003-connectinstance.png?width=5120px)

4. Trong giao diện **MyKey01**

- Chọn **Overview**
- Xem **Last modified user**
- Chọn **Show** Value

![Connect to EC2 instance](/images/4.advancedcloudformation/0004-connectinstance.png?width=5120px)

5. Sao chép value và lưu **cloudformation.pem**

![Connect to EC2 instance](/images/4.advancedcloudformation/0005-connectinstance.png?width=5120px)

6. Sử dụng PuTTY Key Generator load key và **Save private key**

![Connect to EC2 instance](/images/4.advancedcloudformation/0006-connectinstance.png?width=5120px)

7. Sử dụng **PuTTY** để kết nối **EC2 Instance**

![Connect to EC2 instance](/images/4.advancedcloudformation/0007-connectinstance.png?width=5120px)

8. Khi kết nối nhập user name:**ec2-user**

![Connect to EC2 instance](/images/4.advancedcloudformation/0008-connectinstance.png?width=5120px)

9. Sử dụng lệnh **ifconfig -a** để hiển thị thông tin của tất cả giao diện mạng

![Connect to EC2 instance](/images/4.advancedcloudformation/0009-connectinstance.png?width=5120px)

10. Thực hiện **ping amazon.com -c5** để kiểm tra kết nối

![Connect to EC2 instance](/images/4.advancedcloudformation/00010-connectinstance.png?width=5120px)
