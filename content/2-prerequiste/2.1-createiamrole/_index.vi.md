---
title : "Tạo IAM Role"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 2.1. </b> "
---

#### Tạo IAM Role

1. Truy cập giao diện [AWS Management Console](https://console.aws.amazon.com/console/)

![Create IAM Role](/images/2.prerequisite/0001-createiamrole.png?width=5120px)

2. Trong giao diện **IAM**

- Chọn **Roles**
- Chọn **Create role**

![Create IAM Role](/images/2.prerequisite/0002-createiamrole.png?width=5120px)

3. Trong giao diện **Select trusted entity**

- Chọn **Custom trust policy**
- Tại phần **Custom trust policy**, dán đoạn JSON bên dưới vào, nhớ thay ***<ACCOUNT_ID>*** bằng AWS Account ID của bạn:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::<ACCOUNT_ID>:root"
            },
            "Action": "sts:AssumeRole",
        },
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "ec2.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```

{{% notice info %}}
Khi tạo vai trò cho truy cập giữa các tài khoản, quản trị viên sẽ thiết lập mối quan hệ tin tưởng giữa hai tài khoản: tài khoản sở hữu tài nguyên và tài khoản chứa người dùng. Dựa trên điều đó, đoạn JSON trên thể hiện các thực thể đáng tin cậy (trusted entity) như sau:
{{% /notice %}}
> 
> 1. Tài khoản AWS khác:
>    Trong statement đầu tiên, chúng ta thấy:
>    ```json
>    "Principal": {
>        "AWS": "arn:aws:iam::<ACCOUNT_ID>:root"
>    }
>    ```
>    Điều này chỉ định rằng tài khoản AWS có ID là <ACCOUNT_ID> được tin tưởng. Cụ thể, nó cho phép người dùng root của tài khoản đó (và do đó, bất kỳ người dùng nào trong tài khoản đó được cấp quyền phù hợp) đảm nhận vai trò này.
> 
> 2. Dịch vụ AWS:
>    Trong statement thứ hai, chúng ta thấy:
>    ```json
>    "Principal": {
>        "Service": "ec2.amazonaws.com"
>    }
>    ```
>    Điều này chỉ định rằng dịch vụ EC2 của AWS được tin tưởng. Điều này có nghĩa là các instance EC2 có thể đảm nhận vai trò này.
> 
> Các điểm quan trọng khác:
> 
> - Cả hai statement đều cho phép hành động **sts:AssumeRole**, đây là hành động cần thiết để đảm nhận vai trò.
> - Phiên bản của chính sách này là **2012-10-17**, đây là phiên bản tiêu chuẩn cho các chính sách IAM của AWS.
> 
> Tóm lại, chính sách tin tưởng này cho phép:
> 1. Một tài khoản AWS cụ thể (được xác định bởi <ACCOUNT_ID>)...
> 2. Dịch vụ EC2 của AWS...
> 
> ...đảm nhận vai trò (role) này. Điều này tạo ra một mối quan hệ tin tưởng giữa tài khoản sở hữu vai trò và các thực thể được chỉ định, cho phép truy cập giữa các tài khoản và cho phép EC2 instances sử dụng vai trò này.

- Chọn **Next**

![Create IAM Role](/images/2.prerequisite/0003-createiamrole-1.png?width=5120px)

![Create IAM Role](/images/2.prerequisite/0003-createiamrole-2.png?width=5120px)

4. Trong giao diện **Create role**

- Tìm policy **AdministratorAccess**
- Chọn policy **AdministratorAccess**
- Chọn **Next**

![Create IAM Role](/images/2.prerequisite/0004-createiamrole.png?width=5120px)

5. Trong giao diện **Role details**

- **Role name**, nhập `CloudFormation-Role`
- **Description**, nhập `Allows EC2 instances to call AWS services on your role behalf.`

![Create IAM Role](/images/2.prerequisite/0005-createiamrole.png?width=5120px)

6. Trong phần **Step 3: Add tags**

- Nhập **Key**, ví dụ: `Name`
- Nhập **Value**, ví dụ: `CloudFormationWorkshop`
- Chọn **Create role**

![Create IAM Role](/images/2.prerequisite/0006-createiamrole.png?width=5120px)

7. Hoàn thành tạo role

![Create IAM Role](/images/2.prerequisite/0007-createiamrole.png?width=5120px)
