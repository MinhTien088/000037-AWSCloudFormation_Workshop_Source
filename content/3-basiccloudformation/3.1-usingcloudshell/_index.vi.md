---
title : "Sử dụng CloudShell"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 3.1. </b> "
---

#### Sử dụng CloudShell

1. Truy cập vào giao diện [AWS Management Console](https://console.aws.amazon.com/console/)

- Tìm **CloudShell**
- Chọn **CloudShell**

![Using CloudShell](/images/3.basiccloudformation/0001-usingcloudshell.png?width=5120px)

2. Trong giao diện **AWS CloudShell**

- Thường thì khi chọn vào dịch vụ AWS CloudShell sẽ tự động tạo ra môi trường CLI cho chúng ta thực thi lệnh.
- Nhưng trong vài trường hợp, CloudShell sẽ không tự động tạo sẵn, do đó, ta sẽ ấn chọn **Open <REGION> environment** – trong đó <REGION> là tên region khi mở CloudShell.

![Using CloudShell](/images/3.basiccloudformation/0002-usingcloudshell.png?width=5120px)

3. Copy và Paste đoạn lệnh dưới đây vào Terminal của CloudShell để cài đặt các công cụ hỗ trợ xử lý text trên dòng lệnh.

```bash
sudo yum -y install jq gettext bash-completion
```

![Using CloudShell](/images/3.basiccloudformation/0003-usingcloudshell.png?width=5120px)

4. Thực hiện cài đặt tool cfn-lint - là công cụ giúp bạn kiểm tra CloudFormation yaml/json templates và các thông tin khác. Bao gồm kiểm tra các thuộc tính của tài nguyên đã chính xác hay chưa hoặc thông tin cấu hình đã theo best practices hay chưa.

```bash
pip install cfn-lint
```

![Using CloudShell](/images/3.basiccloudformation/0004-usingcloudshell.png?width=5120px)

5. Kiểm tra cài đặt **cfn-lint** thành công bằng cách dùng lệnh sau:

```bash
cfn-lint --version
```

![Using CloudShell](/images/3.basiccloudformation/0005-usingcloudshell.png?width=5120px)

6. Cài đặt **taskcat**

```bash
pip install taskcat
```

![Using CloudShell](/images/3.basiccloudformation/0006-usingcloudshell.png?width=5120px)

7. Chúng ta sẽ cấu hình aws cli sử dụng Region hiện tại.

```bash
export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
export AWS_REGION=<REGION>
export AZS=($(aws ec2 describe-availability-zones --query 'AvailabilityZones[].ZoneName' --output text --region $AWS_REGION))
```

- Thay trường <REGION> thành id của region mà CloudShell đang mở.

![Using CloudShell](/images/3.basiccloudformation/0007-usingcloudshell.png?width=5120px)

8. Chúng ta sẽ lưu các thông tin cấu hình vào bash_profile

```bash
echo "export ACCOUNT_ID=$ACCOUNT_ID" | tee -a ~/.bash_profile
echo "export AWS_REGION=$AWS_REGION" | tee -a ~/.bash_profile
echo "export AZS=${AZS[@]}" | tee -a ~/.bash_profile
aws configure set default.region $AWS_REGION
aws configure get default.region
```

![Using CloudShell](/images/3.basiccloudformation/0008-usingcloudshell.png?width=5120px)

9. Chúng ta sẽ sử dụng câu lệnh để kiểm tra CloudShell đang sử dụng IAM Role có chính xác không.

```bash
aws sts get-caller-identity --query Arn | grep CloudFormation-Role -q && echo "IAM role valid" || echo "IAM role NOT valid"
```

![Using CloudShell](/images/3.basiccloudformation/0009-usingcloudshell.png?width=5120px)
