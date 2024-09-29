---
title : "Create IAM User"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.2. </b> "
---

#### Create IAM User

1. Open interface **[AWS Management Console](https://console.aws.amazon.com/console/)**

- Find **IAM**
- Select **IAM**

![Create IAM User](/images/2.prerequisite/0001-createiamuser.png?width=5120px)

2. In the **IAM** interface

- Select **Users**
- Select **Add users**

![Create IAM User](/images/2.prerequisite/0002-createiamuser.png?width=5120px)

3. In the **Add user** section

- **User name**, enter `CloudFormation-user`
- Select **Access key - Programmatic access**
- Select **Password - AWS Management Console access**
- Select **Custom password**
- Enter password
- Select **Show password**
- Uncheck **Require password reset**
- Select **Next:Permissions**

![Create IAM User](/images/2.prerequisite/0003-createiamuser.png?width=5120px)

4. In the **Set permissions** interface

- Select **Attach existing policies directly**
- Find and select **AdministratorAccess**
- Select **Next:Tags**

![Create IAM User](/images/2.prerequisite/0004-createiamuser.png?width=5120px)

5. In the **Tags** section

- Enter **Key**, example: `Name`
- Enter **Value**, example: `CloudFormationWorkshop`
- Select **Create user**

![Create IAM User](/images/2.prerequisite/0005-createiamuser.png?width=5120px)

6. Create user successfully

- View user information the **Console sign-in details** section
- Select **Download.csv file**
- Select **Return to users list**

![Create IAM User](/images/2.prerequisite/0007-createiamuser.png?width=5120px)

7. So the user has been created successfully

![Create IAM User](/images/2.prerequisite/0008-createiamuser.png?width=5120px)
