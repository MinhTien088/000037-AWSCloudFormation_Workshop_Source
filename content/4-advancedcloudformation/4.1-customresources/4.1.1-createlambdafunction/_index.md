---
title : "Create Lambda Function"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 4.1.1. </b> "
---

#### Create Lambda Function

1. Access the interface [AWS Management Console](https://console.aws.amazon.com/console/)

- Find **IAM**
- Select **IAM**

![Create Lambda Function](/images/4.advancedcloudformation/0001-createlambdafunction.png?width=5120px)

2. In the **IAM** interface

- Select **Policies**
- Select **Create policy**

![Create Lambda Function](/images/4.advancedcloudformation/0002-createlambdafunction.png?width=5120px)

3. In the **Create policy** interface

- Select **JSON**
- Copy and paste the following code:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": "arn:aws:logs:*:*:*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:CreateKeyPair",
                "ec2:DescribeKeyPairs",
                "ssm:PutParameter"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DeleteKeyPair",
                "ssm:DeleteParameter"
            ],
            "Resource": "*"
        }
    ]
}
```

- Select **Next**

![Create Lambda Function](/images/4.advancedcloudformation/0003-createlambdafunction.png?width=5120px)

4. In **Review and create** step

- **Policy name**, enter `ssh-key-gen-policy`
- View services that are **Allow**

![Create Lambda Function](/images/4.advancedcloudformation/0004-createlambdafunction.png?width=5120px)

5. In the **Add tags** section

- Enter **Key**, example: `Name`
- Enter **Value**, example: `CloudFormationWorkshop`
- Select **Create policy**

![Create Lambda Function](/images/4.advancedcloudformation/0005-createlambdafunction.png?width=5120px)

6. Create the **ssh-key-gen-policy** policy successfully

![Create Lambda Function](/images/4.advancedcloudformation/0006-createlambdafunction.png?width=5120px)

7. In the **IAM** interface

- Select **Roles**
- Select **Create role**

![Create Lambda Function](/images/4.advancedcloudformation/0007-createlambdafunction.png?width=5120px)

8. In **Select trusted entity** step

- **Trusted entity type**, select **AWS service**
- **User case**, select **Lambda**
- Select **Next**

![Create Lambda Function](/images/4.advancedcloudformation/0008-createlambdafunction.png?width=5120px)

9. In **Add permissions** step

- Find and select the **ssh-key-gen-policy** policy
- Select **Next**

![Create Lambda Function](/images/4.advancedcloudformation/0009-createlambdafunction.png?width=5120px)

10. In the **Role details** interface

- **Role name**, enter `ssh-key-gen-role`

![Create Lambda Function](/images/4.advancedcloudformation/00010-createlambdafunction.png?width=5120px)

11. Double check and in the **Add tags** section

- Enter **Key**, example: `Name`
- Enter **Value**, example: `CloudFormationWorkshop`
- Select **Create role**

![Create Lambda Function](/images/4.advancedcloudformation/00011-createlambdafunction.png?width=5120px)

12. In the **IAM** interface

- Find **ssh-key-gen-role**
- View created roles

![Create Lambda Function](/images/4.advancedcloudformation/00012-createlambdafunction.png?width=5120px)

13. In the [AWS Management Console](https://console.aws.amazon.com/console/) interface

- Find **Lambda**
- Select **Lambda**

![Create Lambda Function](/images/4.advancedcloudformation/00013-createlambdafunction.png?width=5120px)

14. In the **AWS Lambda** interface

- Select **Functions**
- Select **Create function**

![Create Lambda Function](/images/4.advancedcloudformation/00014-createlambdafunction.png?width=5120px)

15. In the **Create function** interface

- Select **Author from scratch**
- **Function name**, enter `ssh-key-gen`
- **Run time**, select **Python 3.9**
- Select **x86_64**

![Create Lambda Function](/images/4.advancedcloudformation/00015-createlambdafunction.png?width=5120px)

16. In the **Permissions** interface

- Select **Use an existing role**
- Select **ssh-key-gen-role**

![Create Lambda Function](/images/4.advancedcloudformation/00016-createlambdafunction.png?width=5120px)

17. In the function code editing content, enter the following code content:

```python
"""
This lambda implements the custom resource handler for creating an SSH key
and storing in in SSM parameter store.

e.g.

SSHKeyCR:
    Type: Custom::CreateSSHKey
    Version: "1.0"
    Properties:
      ServiceToken: !Ref FunctionArn
      KeyName: MyKey

An SSH key called MyKey will be created.
"""

import os
from json import dumps
import sys
import traceback
import urllib.request

import boto3
from botocore.exceptions import ClientError

def log_exception():
    """Log a stack trace"""
    exc_type, exc_value, exc_traceback = sys.exc_info()
    print(repr(traceback.format_exception(exc_type, exc_value, exc_traceback)))

def send_response(event, context, response):
    """Send a response to CloudFormation to handle the custom resource lifecycle"""
    responseBody = { 
        'Status': response,
        'Reason': 'See details in CloudWatch Log Stream: ' + context.log_stream_name,
        'PhysicalResourceId': context.log_stream_name,
        'StackId': event['StackId'],
        'RequestId': event['RequestId'],
        'LogicalResourceId': event['LogicalResourceId'],
    }

    print('RESPONSE BODY: \n' + dumps(responseBody))

    data = dumps(responseBody).encode('utf-8')
    
    req = urllib.request.Request(event['ResponseURL'], data, headers={'Content-Length': len(data), 'Content-Type': ''})
    req.get_method = lambda: 'PUT'

    try:
        with urllib.request.urlopen(req) as response:
            print(f'response.status: {response.status}, response.reason: {response.reason}')
            print('response from cfn: ' + response.read().decode('utf-8'))
    except urllib.error.URLError:
        log_exception()
        raise Exception('Received non-200 response while sending response to AWS CloudFormation')

    return True

def custom_resource_handler(event, context):
    """
    This function creates a PEM key, commits it as a key pair in EC2, 
    and stores it, encrypted, in SSM.

    To retrieve the key with currect RSA format, you must use the command line: 

    aws ssm get-parameter \
        --name <KEYNAME> \
        --with-decryption \
        --region <REGION> \
        --output text

    Copy the values from (and including) -----BEGIN RSA PRIVATE KEY----- to 
    -----END RSA PRIVATE KEY----- into a file.

    To use it, change the permissions to 600
    Ensure to bundle the necessary packages into the zip stored in S3
    """

    print("Event JSON: \n" + dumps(event))

    request_type = event['RequestType']
    resource_properties = event['ResourceProperties']
    response = 'FAILED'

    if event['ResourceType'] == 'Custom::CreateSSHKey':
        pem_key_name = resource_properties['KeyName']

        ec2 = boto3.client('ec2')
        ssm_client = boto3.client('ssm')

        if request_type == 'Create':
            try:
                # Check if key already exists
                try:
                    ec2.describe_key_pairs(KeyNames=[pem_key_name])
                    print(f"Key pair {pem_key_name} already exists. Skipping creation.")
                    response = 'SUCCESS'
                except ClientError as e:
                    if e.response['Error']['Code'] == 'InvalidKeyPair.NotFound':
                        # Key does not exist, create it
                        print("Creating key name %s" % str(pem_key_name))
                        key = ec2.create_key_pair(KeyName=pem_key_name)
                        key_material = key['KeyMaterial']

                        param = ssm_client.put_parameter(Name=pem_key_name, Value=key_material, Type='SecureString')
                        print(param)
                        print(f'The parameter {pem_key_name} has been created.')

                        response = 'SUCCESS'
                    else:
                        raise e

            except Exception as e:
                print(f'There was an error {e} creating and committing key {pem_key_name} to the parameter store')
                log_exception()
                response = 'FAILED'

            send_response(event, context, response)
            return

        if request_type == 'Update':
            send_response(event, context, 'SUCCESS')
            return

        if request_type == 'Delete':
            try:
                print(f"Deleting key name {pem_key_name}")
                ssm_client.delete_parameter(Name=pem_key_name)
                ec2.delete_key_pair(KeyName=pem_key_name)

                response = 'SUCCESS'
            except Exception as e:
                print(f"There was an error {e} deleting the key {pem_key_name} from SSM Parameter store or EC2")
                log_exception()
                response = 'FAILED'

            send_response(event, context, response)

def lambda_handler(event, context):
    """Lambda handler for the custom resource"""
    try:
        return custom_resource_handler(event, context)
    except Exception:
        log_exception()
        raise
```

Let’s analyze what this code has to do

- **First: Handler function** - every lambda function has a handler function and they are called when any event occurs. The body of the Handler function simply calls another function that contains the content of the specific handler for the event that just occurred.

```python
def lambda_handler(event, context):
    """Lambda handler for the custom resource"""
    try:
        return custom_resource_handler(event, context)
    except Exception:
        log_exception()
        raise
```

- **Second: custom_resource_handler** function - is a function containing detailed handling content when an event occurs. Specifically, the function will perform the determination of the request type and return the response back to CloudFormation.

```python
if request_type == 'Create':
    try:
        # Check if key already exists
        try:
            ec2.describe_key_pairs(KeyNames=[pem_key_name])
            print(f"Key pair {pem_key_name} already exists. Skipping creation.")
            response = 'SUCCESS'
        except ClientError as e:
            if e.response['Error']['Code'] == 'InvalidKeyPair.NotFound':
                # Key does not exist, create it
                print("Creating key name %s" % str(pem_key_name))
                key = ec2.create_key_pair(KeyName=pem_key_name)
                key_material = key['KeyMaterial']

                param = ssm_client.put_parameter(Name=pem_key_name, Value=key_material, Type='SecureString')
                print(param)
                print(f'The parameter {pem_key_name} has been created.')

                response = 'SUCCESS'
            else:
                raise e

    except Exception as e:
        print(f'There was an error {e} creating and committing key {pem_key_name} to the parameter store')
        log_exception()
        response = 'FAILED'

    send_response(event, context, response)
    return
```

- **Third: send_response** function - is a function that sends response results to CloudFormation endpoint based on HTTP PUTS method.

```python
def send_response(event, context, response):
    """Send a response to CloudFormation to handle the custom resource lifecycle"""
    responseBody = { 
        'Status': response,
        'Reason': 'See details in CloudWatch Log Stream: ' + context.log_stream_name,
        'PhysicalResourceId': context.log_stream_name,
        'StackId': event['StackId'],
        'RequestId': event['RequestId'],
        'LogicalResourceId': event['LogicalResourceId'],
    }

    print('RESPONSE BODY: \n' + dumps(responseBody))

    data = dumps(responseBody).encode('utf-8')
    
    req = urllib.request.Request(event['ResponseURL'], data, headers={'Content-Length': len(data), 'Content-Type': ''})
    req.get_method = lambda: 'PUT'

    try:
        with urllib.request.urlopen(req) as response:
            print(f'response.status: {response.status}, response.reason: {response.reason}')
            print('response from cfn: ' + response.read().decode('utf-8'))
    except urllib.error.URLError:
        log_exception()
        raise Exception('Received non-200 response while sending response to AWS CloudFormation')

    return True
```

- Edit the code and select **Deploy**

![Create Lambda Function](/images/4.advancedcloudformation/00017-createlambdafunction.png?width=5120px)

18. After saving the Function, go to **Configuration** tab, select **General configuration** section and hit **Edit** button

![Create Lambda Function](/images/4.advancedcloudformation/00019-createlambdafunction.png?width=5120px)

19. We will increase the timeout of the Lambda function to avoid errors. Set the timeout to **15 minutes** then hit the **Save** button

![Create Lambda Function](/images/4.advancedcloudformation/00020-createlambdafunction.png?width=5120px)

20. After increase the timeout of the Function, copy the Function ARN to a certain memo. This information will be used later in the tutorial.

![Create Lambda Function](/images/4.advancedcloudformation/00018-createlambdafunction.png?width=5120px)
