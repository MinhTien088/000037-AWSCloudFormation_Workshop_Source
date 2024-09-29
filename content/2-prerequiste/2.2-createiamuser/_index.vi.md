---
title : "Tạo IAM User"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 2.2. </b> "
---

#### Tạo IAM User

1. Mở giao diện **[AWS Management Console](https://console.aws.amazon.com/console/)**

- Tìm **IAM**
- Chọn **IAM**

![Create IAM User](/images/2.prerequisite/0001-createiamuser.png?width=5120px)

2. Trong giao diện **IAM**

- Chọn **Users**
- Chọn **Add users**

![Create IAM User](/images/2.prerequisite/0002-createiamuser.png?width=5120px)

3. Trong phần **Add user**

- **User name**, nhập `CloudFormation-user`
- Chọn **Access key - Programmatic access**
- Chọn **Password - AWS Management Console access**
- Chọn **Custom password**
- Nhập password
- Chọn **Show password**
- Bỏ chọn **Require password reset**
- Chọn **Next:Permissions**

![Create IAM User](/images/2.prerequisite/0003-createiamuser.png?width=5120px)

4. Trong giao diện **Set permissions**

- Chọn **Attach existing policies directly**
- Tìm và chọn **AdministratorAccess**
- Chọn **Next:Tags**

![Create IAM User](/images/2.prerequisite/0004-createiamuser.png?width=5120px)

5. Trong phần **Tags**

- Nhập **Key**, ví dụ: `Name`
- Nhập **Value**, ví dụ: `CloudFormationWorkshop`
- Chọn **Create user**

![Create IAM User](/images/2.prerequisite/0005-createiamuser.png?width=5120px)

7. Tạo user thành công

- Xem thông tin user tại phần **Console sign-in details**
- Chọn **Download.csv file**
- Chọn **Return to users list**

![Create IAM User](/images/2.prerequisite/0007-createiamuser.png?width=5120px)

8. Vậy đã tạo user thành công

![Create IAM User](/images/2.prerequisite/0008-createiamuser.png?width=5120px)
