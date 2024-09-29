---
title : "Create CloudFormation Template"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.2. </b> "
---

#### Create CloudFormation Template

One of the keys to working effectively with CloudFormation is knowing how to use the resource reference table on the [AWS Resource and Property Types Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html). Here you can find a list of information and definitions of resources and properties.

1. A CloudFormation template usually consists of 9 parts, details of which you can see in [Template anatomy](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html). Within the workshop, we are only interested in the following 5 main components:

- _The structure of a CloudFormation template_

**AWSTemplateFormatVersion (optional)**: Indicates the version of the template. The latest version is 2010-09-09 and although it was released a long time ago, it is still used until now.

**Description (optional)** describes information related to the template.

**Parameters (optional)**: allows you to add optional values ​​to the template each time you need to create or update the stack.

**Resources (required)**: specify which AWS resources you want to put on the stack for deployment, such as an Amazon EC2 instance or an Amazon S3 bucket.

**Outputs (optional)** defines output values ​​that you can import into other stacks (to create cross-stack references), or as stack responses, or for display on AWS CloudFormation console.

- _Template Parameters_

For each parameter we must specify the name and type for the parameter. Supported parameter types include: String, Number, CommaDelimitedList, List, AWS-Specific Parameter and SSM Parameter. Below are some parameters we use in the workshop. These include

**EC2InstanceType** - defines the Instance type to be used in the template.

**LatestAmiId** - allows to automatically get the latest AMI ID and include it in the template.

**SubnetID** - lists all available Subnet IDs available in the region.

**SourceLocation** - specifies how to use regex in the AllowedPattern property to ensure that the input is appropriate, along with the MinLength and MaxLength values.

**VPCID** - lists available VPCs in the region.

2. Create a .yaml file using Notepad, name it **singleec2instance.yaml** and paste the following content into it

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: "Deploy Single EC2 Linux Instance as part of MGT312 Workshop"
Parameters:
  EC2InstanceType:
    AllowedValues:
      - t3.nano
      - t3.micro
      - t3.small
      - t3.medium
      - t3.large
      - t3.xlarge
      - t3.2xlarge
      - m5.large
      - m5.xlarge
      - m5.2xlarge
    Default: t3.small
    Description: Amazon EC2 instance type
    Type: String
  LatestAmiId:
    Type: 'AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>'
    Default: "/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2"
  SubnetID:
    Description: ID of a Subnet.
    Type: AWS::EC2::Subnet::Id
  SourceLocation:
    Description : The CIDR IP address range that can be used to RDP to the EC2 instances
    Type: String
    MinLength: 9
    MaxLength: 18
    Default: 0.0.0.0/0
    AllowedPattern: "(\\d{1,3})\\.(\\d{1,3})\\.(\\d{1,3})\\.(\\d{1,3})/(\\d{1,2})"
    ConstraintDescription: must be a valid IP CIDR range of the form x.x.x.x/x.
  VPCID:
    Description: ID of the target VPC (e.g., vpc-0343606e).
    Type: AWS::EC2::VPC::Id
```

![Create CloudFormation Template](/images/3.basiccloudformation/0001-createcloudformationtemplate.png?width=5120px)

**Resource: Security Group**

And we have finished setting the parameters so that we can reuse CloudFormaton in the next time. Now we will go to define the required components, which are Resources.

It should be noted that proficient use of the AWS Resource and Property Types Reference documentation plays a very important role in creating a CloudFormation template.

The first resource we add is a Security Group with a rule that allows traffic on port 80. Here we use the *Intrinsic Function Ref* (Intrinsic Function Ref) technique to return the value of VPCID parameter.

AWS CloudFormation provides some built-in functionality that helps you manage Stacks and uses built-in functions in templates to assign values ​​to properties that are only available at runtime.

Copy the following content and paste it after the existing code in the **singleec2instance.yaml** file from the previous step:

```yaml
Resources:
  EC2InstanceSG:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: EC2 Instance Security Group
      VpcId: !Ref 'VPCID'
      SecurityGroupIngress:
      - IpProtocol: tcp
        FromPort: 80
        ToPort: 80
        CidrIp: !Ref SourceLocation
```

**Resource: Instance Role**

In this step we will create a Role that we can assign to EC2 Instance, allowing it to send monitoring information and communicate with AWS Systems Manager Service (SSM). With that, an InstanceProfile is also created and references the Instance Role.

The code also uses the Intrinsic Function Sub technique to replace values ​​in a string with Pseudo parameters like AWS::Region and AWS::Partition.

Dummy parameters are predefined parameters. We will not declare them in the template and use them like a normal parameter.

Copy the following content and paste it after the existing code in the **singleec2instance.yaml** file from the previous step:

```yaml
  SSMInstanceRole:
    Type : AWS::IAM::Role
    Properties:
      Policies:
        - PolicyDocument:
            Version: '2012-10-17'
            Statement:
              - Action:
                  - s3:GetObject
                Resource:
                  - !Sub 'arn:aws:s3:::aws-ssm-${AWS::Region}/*'
                  - !Sub 'arn:aws:s3:::aws-windows-downloads-${AWS::Region}/*'
                  - !Sub 'arn:aws:s3:::amazon-ssm-${AWS::Region}/*'
                  - !Sub 'arn:aws:s3:::amazon-ssm-packages-${AWS::Region}/*'
                  - !Sub 'arn:aws:s3:::${AWS::Region}-birdwatcher-prod/*'
                  - !Sub 'arn:aws:s3:::patch-baseline-snapshot-${AWS::Region}/*'
                Effect: Allow
          PolicyName: ssm-custom-s3-policy
      Path: /
      ManagedPolicyArns:
        - !Sub 'arn:${AWS::Partition}:iam::aws:policy/AmazonSSMManagedInstanceCore'
        - !Sub 'arn:${AWS::Partition}:iam::aws:policy/CloudWatchAgentServerPolicy'
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
        - Effect: "Allow"
          Principal:
            Service:
            - "ec2.amazonaws.com"
            - "ssm.amazonaws.com"
          Action: "sts:AssumeRole"
  SSMInstanceProfile:
    Type: "AWS::IAM::InstanceProfile"
    Properties:
      Roles:
      - !Ref SSMInstanceRole
```

**Resource: EC2 Instance**

Continue building the template with the EC2 Instance definition.

In this step all components including previously configured resources and parameters will be gathered together. We use the Intrinsic Function Reference !Ref technique to assign or create resources and values ​​generated at runtime. Using !Ref also affects the order in which resources are generated by CloudFormation.

Copy the following content and paste it after the existing code in the **singleec2instance.yaml** file from the previous step:

```yaml
  EC2Instance:
    Type: "AWS::EC2::Instance"
    Properties:
      ImageId: !Ref LatestAmiId
      InstanceType: !Ref EC2InstanceType
      IamInstanceProfile: !Ref SSMIstanceProfile
      NetworkInterfaces:
        - DeleteOnTermination: true
          DeviceIndex: '0'
          SubnetId: !Ref 'Subnet'
          GroupSet:
            - !Ref EC2InstanceSG
      Tags:
      - Key: "Name"
        Value: "MGMT312-EC2"
```

**Outputs**

Finally, the Outputs content with the return result is the Private IP address of the Instance - a property value of the Resource - through the use of the GettAtt Intrinsic Function.

Refer to the AWS Resource and Property Types Reference documentation to find additional information that can be obtained with the GetAtt or !Ref function. Copy the following content and paste it after the existing code in the **singleec2instance.yaml** file from the previous step:

```yaml
Outputs:
  EC2InstancePrivateIP:
    Value: !GetAtt 'EC2Instance.PrivateIp'
    Description: Private IP for EC2 Instances
```

- Check the template one last time

```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: "Deploy Single EC2 Linux Instance"
Parameters:
  EC2InstanceType:
    AllowedValues:
      - t3.nano
      - t3.micro
      - t3.small
      - t3.medium
      - t3.large
      - t3.xlarge
      - t3.2xlarge
      - m5.large
      - m5.xlarge
      - m5.2xlarge
    Default: t3.small
    Description: Amazon EC2 instance type
    Type: String
  LatestAmiId:
    Type: 'AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>'
    Default: "/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2"
  SubnetID:
    Description: ID of a Subnet.
    Type: AWS::EC2::Subnet::Id
  SourceLocation:
    Description : The CIDR IP address range that can be used to RDP to the EC2 instances
    Type: String
    MinLength: 9
    MaxLength: 18
    Default: 0.0.0.0/0
    AllowedPattern: "(\\d{1,3})\\.(\\d{1,3})\\.(\\d{1,3})\\.(\\d{1,3})/(\\d{1,2})"
    ConstraintDescription: must be a valid IP CIDR range of the form x.x.x.x/x.
  VPCID:
    Description: ID of the target VPC (e.g., vpc-0343606e).
    Type: AWS::EC2::VPC::Id

Resources:

  EC2InstanceSG:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: EC2 Instance Security Group
      VpcId: !Ref 'VPCID'
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: !Ref SourceLocation

  SSMInstanceRole:
    Type : AWS::IAM::Role
    Properties:
      Policies:
        - PolicyDocument:
            Version: '2012-10-17'
            Statement:
              - Action:
                  - s3:GetObject
                Resource:
                  - !Sub 'arn:aws:s3:::aws-ssm-${AWS::Region}/*'
                  - !Sub 'arn:aws:s3:::aws-windows-downloads-${AWS::Region}/*'
                  - !Sub 'arn:aws:s3:::amazon-ssm-${AWS::Region}/*'
                  - !Sub 'arn:aws:s3:::amazon-ssm-packages-${AWS::Region}/*'
                  - !Sub 'arn:aws:s3:::${AWS::Region}-birdwatcher-prod/*'
                  - !Sub 'arn:aws:s3:::patch-baseline-snapshot-${AWS::Region}/*'
                Effect: Allow
          PolicyName: ssm-custom-s3-policy
      Path: /
      ManagedPolicyArns:
        - !Sub 'arn:${AWS::Partition}:iam::aws:policy/AmazonSSMManagedInstanceCore'
        - !Sub 'arn:${AWS::Partition}:iam::aws:policy/CloudWatchAgentServerPolicy'
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: "Allow"
            Principal:
              Service:
              - "ec2.amazonaws.com"
              - "ssm.amazonaws.com"
            Action: "sts:AssumeRole"

  SSMInstanceProfile:
    Type: "AWS::IAM::InstanceProfile"
    Properties:
      Roles:
        - !Ref SSMInstanceRole

  EC2Instance:
    Type: "AWS::EC2::Instance"
    Properties:
      ImageId: !Ref LatestAmiId
      InstanceType: !Ref EC2InstanceType
      IamInstanceProfile: !Ref SSMInstanceProfile
      NetworkInterfaces:
        - DeleteOnTermination: true
          DeviceIndex: '0'
          SubnetId: !Ref 'Subnet'
          GroupSet:
            - !Ref EC2InstanceSG
      Tags:
        - Key: "Name"
          Value: "MGMT312-EC2"

Outputs:
  EC2InstancePrivateIP:
    Value: !GetAtt 'EC2Instance.PrivateIp'
    Description: Private IP for EC2 Instances
```

3. Upload the file to CloudShell by selecting **Actions** > **Upload file** > Choose the **singleec2instance.yaml** file you just created.

![Create CloudFormation Template](/images/3.basiccloudformation/0002-createcloudformationtemplate.png?width=5120px)

- Wait for a moment and you will see a notification that the file has been uploaded successfully.

![Create CloudFormation Template](/images/3.basiccloudformation/0003-createcloudformationtemplate.png?width=5120px)

4. To reduce the burden of error checking of apple template code, we can use some automated testing tools to validate the syntax used in the template. For this CloudFormation workshop, we use **cfn-lint**

- Run the following command:

```bash
cfn-lint singleec2instance.yaml
```

- Easy to see, there was an error with the CloudFormation template, specifically line 97, we used the wrong Subnet value. The cfn-lint tool really did a great job, detecting errors in syntax and information in the template.

![Create CloudFormation Template](/images/3.basiccloudformation/0004-createcloudformationtemplate.png?width=5120px)

5. Modify the template by changing the Subnet value to SubnetID, and then run the cfn-lint command again.

![Create CloudFormation Template](/images/3.basiccloudformation/0005-createcloudformationtemplate.png?width=5120px)

6. The result is that the command runs successfully and there are no errors, other than a warning about python version. We can ignore them because we are simply doing a workshop.

![Create CloudFormation Template](/images/3.basiccloudformation/0006-createcloudformationtemplate.png?width=5120px)

7. Create CloudFormation Stack To create CloudFormation Stack, Cloud9 server is required to access and use EC2 and IAM services, so it is necessary to implement additional policy for IAM Role being attached to Cloud9 server. For example, adding necessary policies such as **AmazonEC2FullAccess**, **IAMFullAccess**

- Access the page [AWS Management Console](https://console.aws.amazon.com/console/)
- Find **IAM**
- Select **IAM**

![Create CloudFormation Template](/images/3.basiccloudformation/0007-createcloudformationtemplate.png?width=5120px)

8. In the **IAM** interface

- Select **Roles**
- Search **CloudFormation-Role**
- Select Role name **CloudFormation-Role**

![Create CloudFormation Template](/images/3.basiccloudformation/0008-createcloudformationtemplate.png?width=5120px)

9. In the **CloudFormation-Role** interface

- Select **Add permissions**
- Select **Attach policies**

![Create CloudFormation Template](/images/3.basiccloudformation/0009-createcloudformationtemplate.png?width=5120px)

10. In **Attach policy to CloudFormation-Role** step

- Find **AmazonEC2FullAccess**
- Select **AmazonEC2FullAccess**
- Select **Attach policies**

![Create CloudFormation Template](/images/3.basiccloudformation/00010-createcloudformationtemplate.png?width=5120px)

11. Successful **Attach policies** interface

![Create CloudFormation Template](/images/3.basiccloudformation/00011-createcloudformationtemplate.png?width=5120px)

12. Continue implementing **Attach policies**

- Select **Add permissions**
- Select **Attach policies**

![Create CloudFormation Template](/images/3.basiccloudformation/00012-createcloudformationtemplate.png?width=5120px)

13. In **Attach policy to CloudFormation-Role** step

- Find **IAMFullAccess**
- Select **IAMFullAccess**
- Select **Attach policies**

![Create CloudFormation Template](/images/3.basiccloudformation/00013-createcloudformationtemplate.png?width=5120px)

14. Interface after **Attach policies** is successful

![Create CloudFormation Template](/images/3.basiccloudformation/00014-createcloudformationtemplate.png?width=5120px)

15. To prepare to create **CloudFormation Template** we need to prepare **VPC** and **Public subnet**

- Access to [AWS Management Console](https://aws.amazon.com/console/)
- Find **VPC**
- Select **VPC**

![Create CloudFormation Template](/images/3.basiccloudformation/00015-createcloudformationtemplate.png?width=5120px)

16. In the **VPC** interface

- Select **Your VPC**
- Select the **VPC** you want to use
- Copy **VPC ID** to use to create **CloudFormation Template**
- The same subnet also repeats

![Create CloudFormation Template](/images/3.basiccloudformation/00016-createcloudformationtemplate.png?width=5120px)

17. In the **VPC** interface

- Select **Subnets**
- Select the subnet you want to use (Public Subnet)
- Copy **Subnet ID**

![Create CloudFormation Template](/images/3.basiccloudformation/00017-createcloudformationtemplate.png?width=5120px)

18. After the Cloud9 server has enough permission to create Resources with CloudFormation, on the terminal window, run the command:

- You replace **VPC ID** and **Subnet ID** in the command.
- **ParameterValue** of **ParameterKey=SubnetID** replace with **Subnet ID** of step 17
- **ParameterValue** of **ParameterKey=VPCID** replaced with **VPCID** of step 16

```bash
aws cloudformation create-stack --stack-name asg-cloudformation-stack --template-body file://singleec2instance.yaml --parameters ParameterKey=SubnetID,ParameterValue=<SubnetID> ParameterKey=VPCID,ParameterValue=<VPCID> --capabilities CAPABILITY_IAM --region us-east-1
```

![Create CloudFormation Template](/images/3.basiccloudformation/00018-createcloudformationtemplate.png?width=5120px)

19. Conduct initialization test **CloudFormation Template**

- Go to [AWS Management Console](https://aws.amazon.com/console/)
- Find **CloudFormation**
- Select **CloudFormation**

![Create CloudFormation Template](/images/3.basiccloudformation/00019-createcloudformationtemplate.png?width=5120px)

20. In the **CloudFormation** interface

- Select **Stack details**
- Select Stack named **asg-cloudformation-stack**
- Select **Events**
- View initialization events
- When **Status** changes to **CREATE_COMPLETE**, the initialization is successful

![Create CloudFormation Template](/images/3.basiccloudformation/00020-createcloudformationtemplate.png?width=5120px)

21. In the **CloudFormation** interface

- Select **Stack details**
- Select the stack just created
- Select **Resource**
- View information about **Resource** successfully initialized

![Create CloudFormation Template](/images/3.basiccloudformation/00021-createcloudformationtemplate.png?width=5120px)

22. In the **CloudFormation** interface

- Select **Stack details**
- Select the stack just created
- Select **Parameters**
- View information of **Key-Value** initializers

![Create CloudFormation Template](/images/3.basiccloudformation/00022-createcloudformationtemplate.png?width=5120px)

23. In the **CloudFormation** interface

- Select **Stack details**
- Select the stack just created
- Select **Outputs**
- Result received 1 EC2Instance

![Create CloudFormation Template](/images/3.basiccloudformation/00023-createcloudformationtemplate.png?width=5120px)

24. To verify **EC2 Instance** is initialized

- Access the page [AWS Management Console](https://aws.amazon.com/console/)
- Find **EC2**
- Select **EC2**

![Create CloudFormation Template](/images/3.basiccloudformation/00024-createcloudformationtemplate.png?width=5120px)

25. In the **EC2** interface

- Select **Instances**
- Select the instance to be initialized
- Select **Details** to see detailed information

![Create CloudFormation Template](/images/3.basiccloudformation/00025-createcloudformationtemplate.png?width=5120px)
