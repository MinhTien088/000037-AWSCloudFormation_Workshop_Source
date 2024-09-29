---
title : "Using CloudShell"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.1. </b> "
---

#### Using CloudShell

1. Access the [AWS Management Console](https://console.aws.amazon.com/console/) interface

- Search **CloudShell**
- Select **CloudShell**

![Using CloudShell](/images/3.basiccloudformation/0001-usingcloudshell.png?width=5120px)

2. In the **AWS CloudShell** interface

- Usually, when selecting the AWS CloudShell service, it automatically creates a CLI environment for us to execute commands.
- However, in some cases, CloudShell may not automatically create one. In such instances, we will click on **Open <REGION> environment** – where <REGION> is the name of the region when opening CloudShell.

![Using CloudShell](/images/3.basiccloudformation/0002-usingcloudshell.png?width=5120px)

3. Copy and paste the following command into the CloudShell Terminal to install tools that support text processing on the command line.

```bash
sudo yum -y install jq gettext bash-completion
```

![Using CloudShell](/images/3.basiccloudformation/0003-usingcloudshell.png?width=5120px)

4. Install tool cfn-lint - a tool to help you check CloudFormation yaml/json templates and other information. This includes checking that the resource’s properties are correct or that the configuration information is following best practices.

```bash
pip install cfn-lint
```

![Using CloudShell](/images/3.basiccloudformation/0004-usingcloudshell.png?width=5120px)

5. Check the **cfn-lint** installation is successful using the following command:

```bash
cfn-lint --version
```

![Using CloudShell](/images/3.basiccloudformation/0005-usingcloudshell.png?width=5120px)

6. Install **taskcat**

```bash
pip install taskcat
```

![Using CloudShell](/images/3.basiccloudformation/0006-usingcloudshell.png?width=5120px)

7. We will configure the AWS CLI to use the current Region.

```bash
export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
export AWS_REGION=<REGION>
export AZS=($(aws ec2 describe-availability-zones --query 'AvailabilityZones[].ZoneName' --output text --region $AWS_REGION))
```

- Replace the <REGION> field with the ID of the region where CloudShell is currently open.

![Using CloudShell](/images/3.basiccloudformation/0007-usingcloudshell.png?width=5120px)

8. We will save the configuration information to bash_profile

```bash
echo "export ACCOUNT_ID=$ACCOUNT_ID" | tee -a ~/.bash_profile
echo "export AWS_REGION=$AWS_REGION" | tee -a ~/.bash_profile
echo "export AZS=${AZS[@]}" | tee -a ~/.bash_profile
aws configure set default.region $AWS_REGION
aws configure get default.region
```

![Using CloudShell](/images/3.basiccloudformation/0008-usingcloudshell.png?width=5120px)

9. We will use a command to check if CloudShell is using the correct IAM Role.

```bash
aws sts get-caller-identity --query Arn | grep CloudFormation-Role -q && echo "IAM role valid" || echo "IAM role NOT valid"
```

![Using CloudShell](/images/3.basiccloudformation/0009-usingcloudshell.png?width=5120px)
