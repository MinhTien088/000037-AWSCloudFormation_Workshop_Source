---
title : "Create IAM Role"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 2.1. </b> "
---

#### Create IAM Role

1. Access the [AWS Management Console](https://console.aws.amazon.com/console/)

![Create IAM Role](/images/2.prerequisite/0001-createiamrole.png?width=5120px)

2. In the **IAM** interface

- Select **Roles**
- Select **Create role**

![Create IAM Role](/images/2.prerequisite/0002-createiamrole.png?width=5120px)

3. In the **Select trusted entity** interface

- Select **Custom trust policy**
- In the **Custom trust policy** section, paste this JSON into it, remember to replace ***<ACCOUNT_ID>*** with your actual AWS Account ID:

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
When creating a role for cross-account access, the administrator establishes a trust relationship between two accounts: the account that owns the resources and the account that contains the users. Based on this, the JSON snippet above represents the trusted entities as follows:
{{% /notice %}}
> 
> 1. Another AWS account:
>    In the first statement, we see:
>    ```json
>    "Principal": {
>        "AWS": "arn:aws:iam::<ACCOUNT_ID>:root"
>    }
>    ```
>    This specifies that the AWS account with the ID <ACCOUNT_ID> is trusted. Specifically, it allows the root user of that account (and therefore, any user in that account granted appropriate permissions) to assume this role.
> 
> 2. AWS Service:
>    In the second statement, we see:
>    ```json
>    "Principal": {
>        "Service": "ec2.amazonaws.com"
>    }
>    ```
>    This specifies that the EC2 service of AWS is trusted. This means that EC2 instances can assume this role.
> 
> Other important points:
> 
> - Both statements allow the **sts:AssumeRole** action, which is the necessary action to assume the role.
> - The version of this policy is **2012-10-17**, which is the standard version for AWS IAM policies.
> 
> In summary, this trust policy allows:
> 1. A specific AWS account (identified by <ACCOUNT_ID>)...
> 2. The AWS EC2 service...
> 
> ...to assume this role. This creates a trust relationship between the account owning the role and the specified entities, allowing cross-account access and enabling EC2 instances to use this role.

- Select **Next**

![Create IAM Role](/images/2.prerequisite/0003-createiamrole-1.png?width=5120px)

![Create IAM Role](/images/2.prerequisite/0003-createiamrole-2.png?width=5120px)

4. In the **Create role** interface

- Find the policy **AdministratorAccess**
- Select the policy **AdministratorAccess**
- Select **Next**

![Create IAM Role](/images/2.prerequisite/0004-createiamrole.png?width=5120px)

5. In the **Role details** interface

- **Role name**, enter `CloudFormation-Role`
- **Description**, enter `Allows EC2 instances to call AWS services on your role behalf.`

![Create IAM Role](/images/2.prerequisite/0005-createiamrole.png?width=5120px)

6. In the **Step 3: Add tags** section

- Enter **Key**, example: `Name`
- Enter **Value**, example: `CloudFormationWorkshop`
- Select **Create role**

![Create IAM Role](/images/2.prerequisite/0006-createiamrole.png?width=5120px)

7. Complete role creation

![Create IAM Role](/images/2.prerequisite/0007-createiamrole.png?width=5120px)
