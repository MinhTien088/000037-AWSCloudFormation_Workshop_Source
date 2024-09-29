---
title : "Switch Role"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 2.3. </b> "
---

#### Switch Role

1. Mở giao diện **[AWS Management Console](https://console.aws.amazon.com/console/)**

- Tìm **IAM**
- Chọn **IAM**

![Create IAM User](/images/2.prerequisite/0001-switchrole.png?width=5120px)

2. Trong giao diện **IAM**

- Chọn **Users**
- Chọn user **CloudFormation-user** vừa tạo ở bước trước

![role](/images/2.prerequisite/0002-switchrole.png?width=5120px)

3. Tại giao diện user **CloudFormation-user**

- Chọn **Permissions**
- Chọn **Add permissions**
- Chọn **Create inline policy**

![role](/images/2.prerequisite/0003-switchrole.png?width=5120px)

4. Tại giao diện **Specify permissions**

- Tại phần **Policy editor**, chuyển sang chế độ **JSON**
- Dán đoạn JSON bên dưới vào, nhớ thay ***<ACCOUNT_ID>*** bằng AWS Account ID của bạn:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "sts:AssumeRole",
            "Resource": "arn:aws:iam::<ACCOUNT_ID>:role/CloudFormation-Role"
        }
    ]
}
```

- Chọn **Next**

![role](/images/2.prerequisite/0004-switchrole.png?width=5120px)

5. Tại phần **Policy details**

- **Policy name**, nhập `CloudFormation-switchrole`
- Chọn **Create policy**

![role](/images/2.prerequisite/0005-switchrole.png?width=5120px)

6. Hoàn thành việc thêm một Policy vào user **CloudFormation-user**

![role](/images/2.prerequisite/0006-switchrole.png?width=5120px)

7. Tiến hành switch role để bắt đầu bài workshop

![role](/images/2.prerequisite/0007-switchrole.png?width=5120px)

8. Tại giao diện **Switch Role**

- **Account ID**, nhập AWS Account ID của bạn
- **IAM role name**, nhập `CloudFormation-Role`, nhớ thay ***<ACCOUNT_ID>*** bằng AWS Account ID của bạn
- Chọn **Switch Role**

![role](/images/2.prerequisite/0008-switchrole.png?width=5120px)

9. Hoàn thành bước switch role

- Tại góc trái trên, ta sẽ thấy **CloudFormation-Role @ <ACCOUNT_ID>**, nghĩa là đã switch role thành công

![role](/images/2.prerequisite/0009-switchrole.png?width=5120px)
