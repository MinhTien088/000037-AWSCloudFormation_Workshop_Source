---
title : "Tạo Stack"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 4.1.2. </b> "
---

#### Tạo Stack

1. Truy cập vào [AWS Management Console](https://console.aws.amazon.com/console/)

- Tìm **CloudFormaton**
- Chọn **CloudFormation**

![Create Stack](/images/4.advancedcloudformation/0001-createstack.png?width=5120px)

2. Trong giao diện **CloudFormation**

- Chọn **Stack**
- Chọn **Create stack**
- Chọn **With new resources (standard)**

![Create Stack](/images/4.advancedcloudformation/0002-createstack.png?width=5120px)

3. Trong giao diện **Create stack**

- Tạo một file **custom_resource_cfn_mr.yml**
- Sau đó sao chép và dán nội dung mã này vào file đồng thời lưu lại:

```yaml
Parameters:
  SourceAccessCIDR:
    Type: String
    Description: The CIDR IP range that is permitted to access the instance. We recommend that you set this value to a trusted IP range.
    Default: 0.0.0.0/0
  SSHKeyName:
    Type: String
    Description: The name of the key that will be created
    Default: MyKey01
  AMIID:
    Type: 'AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>'
    Description: The AMI ID that will be used to create EC2 instance
    Default: "/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2"
  VPCPublicSubnet:
    Type: AWS::EC2::Subnet::Id
    Description: Choose a public subnet in the selected VPC
  VPC:
    Type: AWS::EC2::VPC::Id
    Description: The VPC in which to launch the EC2 instance. We recommend you choose your default VPC.
  FunctionArn:
    Type: String
    Description: The ARN of the lambda function that implements the custom resource

Resources:
  SSHKeyCR:
    Type: Custom::CreateSSHKey
    Properties:
      ServiceToken: !Ref FunctionArn
      KeyName: !Ref SSHKeyName

  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      KeyName: !Ref SSHKeyName
      InstanceType: t2.micro
      ImageId: !Ref AMIID
      IamInstanceProfile: !Ref EC2InstanceProfile
      SecurityGroupIds:
        - !Ref EC2InstanceSG
      SubnetId: !Ref VPCPublicSubnet
      Tags:
        - Key: Name
          Value: Cfn-Workshop-Reinvent-2018-Lab1
    DependsOn: SSHKeyCR

  EC2InstanceProfile:
    Type: AWS::IAM::InstanceProfile
    Properties:
      Roles:
        - Ref: EC2InstanceRole
      Path: "/"
    DependsOn: EC2InstanceRole

  EC2InstanceRole:
    Type: AWS::IAM::Role
    Properties:
      Policies:
        - PolicyDocument:
            Version: '2012-10-17'
            Statement:
              - Action:
                  - s3:GetObject
                Resource: "*"
                Effect: Allow
          PolicyName: s3-policy
        - PolicyDocument:
            Version: '2012-10-17'
            Statement:
              - Action:
                  - logs:CreateLogStream
                  - logs:GetLogEvents
                  - logs:PutLogEvents
                  - logs:DescribeLogGroups
                  - logs:DescribeLogStreams
                  - logs:PutRetentionPolicy
                  - logs:PutMetricFilter
                  - logs:CreateLogGroup
                Resource:
                  - arn:aws:logs:*:*:*
                  - arn:aws:s3:::*
                Effect: Allow
          PolicyName: cloudwatch-logs-policy
        - PolicyDocument:
            Version: '2012-10-17'
            Statement:
              - Action:
                  - ec2:AssociateAddress
                  - ec2:DescribeAddresses
                Resource: "*"
                Effect: Allow
          PolicyName: eip-policy
      Path: "/"
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
          - Action:
              - sts:AssumeRole
            Principal:
              Service:
                - ec2.amazonaws.com
            Effect: Allow

  EC2InstanceSG:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: This should allow you to SSH to the instance from your location
      VpcId: !Ref VPC
      Tags:
        - Key: Name
          Value: !Sub ${AWS::StackName}
      SecurityGroupIngress:
        - Description: This should allow you to SSH from your location into an EC2 instance
          CidrIp: !Ref SourceAccessCIDR
          FromPort: 22
          IpProtocol: tcp
          ToPort: 22

Outputs:
  MyEC2InstanceDNSName:
    Description: The DNSName of the new EC2 instance
    Value: !GetAtt MyEC2Instance.PublicDnsName
  MyEC2InstancePublicIP:
    Description: The Public IP address of the newly created EC2 instance
    Value: !GetAtt MyEC2Instance.PublicIp
```

- Chọn **Template is ready**
- Chọn **Upload a template file**
- Chọn **Choose file**
- Chọn **custom_resource_cfn_mr.yml**
- Chọn **Next**

![Create Stack](/images/4.advancedcloudformation/0003-createstack.png?width=5120px)

4. Phần **Stack name**, nhập `ssh-key-gen-cr`

![Create Stack](/images/4.advancedcloudformation/0004-createstack.png?width=5120px)

5. Trong giao diện **Create satck**

- **AMI ID**, để nguyên mặc định
- **FunctionArn**, nhập Arn của Lambda function đã tạo
- **SSHKeyName**, nhập tên key pair sẽ tạo
- **SourceAccessCIDR**, nhập `0.0.0.0/0`
- Chọn **VPC**
- Chọn **Public Subnet**

![Create Stack](/images/4.advancedcloudformation/0008-createstack.png?width=5120px)

6. Trong phần **Add tags**

- Nhập **Key**, ví dụ: `Name`
- Nhập **Value**, ví dụ: `CloudFormationWorkshop`
- Chọn **Next**

![Create Stack](/images/4.advancedcloudformation/0009-createstack.png?width=5120px)

7. Trong giao diện **Create stack**

- Chọn **I acknowledge that AWS CloudFormation might create IAM resources**
- Chọn **Create stack**

![Create Stack](/images/4.advancedcloudformation/00010-createstack.png?width=5120px)

8. Trong giao diện **CloudFormation**

- Chọn **Stack**
- Chọn **ssh-key-gen-cr** stack
- Chọn **Event** để xem các event khởi tạo. Qúa trình khởi tạo chuyển sang **CREATE_COMPLETE** là thành công

![Create Stack](/images/4.advancedcloudformation/00011-createstack.png?width=5120px)

9. Trong giao diện **CloudFormation**

- Chọn **Resources**
- Xem các resource đã khởi tạo

![Create Stack](/images/4.advancedcloudformation/00012-createstack.png?width=5120px)

10. Trong giao diện **CloudFormation**

- Chọn **Parameters**
- Xem thông tin **Key-Value**

![Create Stack](/images/4.advancedcloudformation/00013-createstack.png?width=5120px)

11. Trong giao diện **CloudFormation**

- Chọn **Outputs**
- Xem instance đã được khởi tạo

![Create Stack](/images/4.advancedcloudformation/00014-createstack.png?width=5120px)

12. Trong giao diện [AWS Management Console](https://console.aws.amazon.com/console/)

- Tìm **EC2**
- Chọn **EC2**

![Create Stack](/images/4.advancedcloudformation/00015-createstack.png?width=5120px)

13. Trong giao diện **EC2**

- Chọn **Instances**
- Chọn instance vừa tạo
- Chọn **Details**
- Chọn **public IPv4 address**
- Xem **IAM Role**

![Create Stack](/images/4.advancedcloudformation/00016-createstack.png?width=5120px)
