---
title : "Clean up resources"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 5. </b> "
---

#### Delete CloudShell Environment

1. Select **Region** containing the environment to delete
2. In the **CloudShell** interface, choose **Actions**
3. Select **Delete**![Clean](/images/5.cleanup/0001-cleanup.png?width=5120px)
4. Verify environment deletion by entering **delete** and selecting **Delete**![Clean](/images/5.cleanup/0002-cleanup.png?width=5120px)

#### Delete a stack on the AWS CloudFormation console

1. Go to [AWS CloudFormation](https://console.aws.amazon.com/cloudformation/) page
2. In the **CloudFormation** interface, select **Stack**
3. Select **Stack** to delete
4. Select **Delete**
5. Verify clearing stack
6. Wait a few minutes for the stack to change state to **DELETE_COMPLETE** to be deleted successfully![Clean](/images/5.cleanup/0003-cleanup.png?width=5120px)

#### Delete a stack set

1. Go to [AWS CloudFormation](https://console.aws.amazon.com/cloudformation/) page
2. Select **StackSets**
3. Select **Stackset** to delete
4. Select **Actions** then select **Delete StackSet**
5. Verify delete stack set and select **Delete StackSet**

#### Delete Lambda Functions

1. Go to [Lambda](https://console.aws.amazon.com/lambda/)
2. Select **Functions**
3. Select **funtion(s)** to delete
4. Select **Actions** then select **Delete**
5. Verify delete funtion(s) and select **Delete**

#### Delete User

1. Go to [AWS IAM](https://console.aws.amazon.com/iam/)
2. Select **Users**
3. Select **User** to delete
4. Verify delete user and select **Delete user**

#### Delete Role

1. Go to [AWS IAM](https://console.aws.amazon.com/iam/)
2. Select **Roles**
3. Select **Roles** to delete
4. Verify delete role and select **Delete**
