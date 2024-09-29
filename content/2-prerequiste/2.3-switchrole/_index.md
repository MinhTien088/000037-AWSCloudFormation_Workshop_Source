---
title : "Switch Role"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 2.3. </b> "
---

#### Switch Role

1. Access the **[AWS Management Console](https://console.aws.amazon.com/console/)** interface

- Search for **IAM**
- Select **IAM**

![Create IAM User](/images/2.prerequisite/0001-switchrole.png?width=5120px)

2. In the **IAM** interface

- Select **Users**
- Choose the **CloudFormation-user** created in the previous step

![role](/images/2.prerequisite/0002-switchrole.png?width=5120px)

3. In the **CloudFormation-user** interface

- Select **Permissions**
- Choose **Add permissions**
- Select **Create inline policy**

![role](/images/2.prerequisite/0003-switchrole.png?width=5120px)

4. In the **Specify permissions** interface

- In the **Policy editor** section, switch to **JSON** mode
- Paste the JSON below, remember to replace ***<ACCOUNT_ID>*** with your AWS Account ID:

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

- Choose **Next**

![role](/images/2.prerequisite/0004-switchrole.png?width=5120px)

5. In the **Policy details** section

- **Policy name**, enter `CloudFormation-switchrole`
- Choose **Create policy**

![role](/images/2.prerequisite/0005-switchrole.png?width=5120px)

6. Complete adding a Policy to the **CloudFormation-user**

![role](/images/2.prerequisite/0006-switchrole.png?width=5120px)

7. Proceed to switch roles to begin the workshop

![role](/images/2.prerequisite/0007-switchrole.png?width=5120px)

8. In the **Switch Role** interface

- **Account ID**, enter your AWS Account ID
- **IAM role name**, enter `CloudFormation-Role`, remember to replace ***<ACCOUNT_ID>*** with your AWS Account ID
- Choose **Switch Role**

![role](/images/2.prerequisite/0008-switchrole.png?width=5120px)

9. Complete the role switch

- In the top left corner, you will see **CloudFormation-Role @ <ACCOUNT_ID>**, which means the role switch was successful

![role](/images/2.prerequisite/0009-switchrole.png?width=5120px)
