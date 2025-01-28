# Lambda Function Using SAM Template file (manual)

**sam deploy --guided --template template.yaml** is the command you enter at the command line.

sam deploy --guided --template should be provided as is.

template.yaml can be replaced with your specific file name.

The output starts at Configuring SAM deploy.

In the output, ENTER and y indicate replaceable values that you provide.

### Pre-requisite:

1) AWS SAM-CLI ref: https://github.com/aws/aws-sam-cli
```
sam --version
```
2) AWS-CLI ref: https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
```
aws --version
```
3) Python ref: https://www.digitalocean.com/community/tutorials/install-python-windows-10
```
python --version or python3 --version

pip --version
```

**To create and deploy a Lambda function using AWS SAM (Serverless Application Model) from a template.yml file, follow these steps:**

### Step 1: Set up Your SAM Template (template.yml)

The template.yml file defines the AWS resources required for your serverless application. Here's an example template.yml for a simple Lambda function written in Python:
```
AWSTemplateFormatVersion: '2010-09-09'
Transform: 'AWS::Serverless-2016-10-31'
Resources:
  MyLambdaFunction:
    Type: 'AWS::Serverless::Function'
    Properties:
      Handler: app.lambda_handler
      Runtime: python3.8
      CodeUri: .
      MemorySize: 128
      Timeout: 3
      Environment:
        Variables:
          STAGE: dev

Handler: The function name within your code that AWS Lambda calls.
Runtime: The runtime for your Lambda function (in this case, Python 3.8).
CodeUri: The location of your Lambda function code (here it's the current directory .).
MemorySize: The amount of memory allocated for your Lambda function.
Timeout: The maximum time your function is allowed to run before being terminated.
```

### Step 2: Create the Lambda Function Code

Next, create the Lambda function code in a file named app.py (or any name you specify in template.yml).

```
import json

def lambda_handler(event, context):
    return {
        'statusCode': 200,
        'body': json.dumps('Hello from Lambda!')
    }
```
### Step 3: Initialize SAM Project (If Needed)

If you haven’t already set up a SAM project, you can initialize one by running:
```
$ sam init
```
Follow the prompts to set up your project. You can select a runtime (e.g., Python) and a template (e.g., Hello World).

### Step 4: Build Your SAM Application

Before deploying, you need to build the application with the SAM CLI:
```
$ sam build
```
This will package your Lambda function and prepare it for deployment.

### Step 5: Deploy Your SAM Application

Now you can deploy your application to AWS:
```
$ sam deploy --guided
```
This command will ask you a few questions about your deployment (e.g., stack name, AWS region, whether to save these preferences for future use).

The deploy command does the following:

1. Packages your Lambda function and other resources defined in the template.yml.
2. Uploads the packaged code to an S3 bucket.
3. Deploys the CloudFormation stack to create the Lambda function, along with any other resources (e.g., API Gateway, DynamoDB).

### Step 6: Verify the Deployment
Once the deployment is complete, you can verify that the Lambda function has been created by checking the AWS Console or by invoking the Lambda function using the AWS CLI:
```
$ aws lambda invoke --function-name MyLambdaFunction output.txt
```
This will invoke the Lambda function and save the result to output.txt.

That’s the basic flow to create and deploy a Lambda function from a template.yml file using AWS SAM. Let me know if you need more details on any of the steps!


## Key recommendations:

1) you can remove the sam init step. You should have your template.yml file prepared beforehand with the correct Lambda function configuration.

2) Use the $TEMPLATE_FILE variable instead of hardcoding template.yaml.

3) Remove the --guided flag from sam deploy to avoid interactive prompts.

4) Add the --no-confirm-changeset and --no-fail-on-empty-changeset flags to sam deploy for non-interactive deployment.

5) Use the same sam deploy command for both creating and updating the function.

6) Ensure that the IAM role used for deployment has the following permissions:
```
iam:CreateRole
iam:PutRolePolicy
iam:AttachRolePolicy
cloudformation:*
s3:*
lambda:*
apigateway:*
tag:* (for resource tagging)
```
7) If you want to use a pre-existing IAM role for your Lambda function instead of creating a new one, modify your template.yml to specify the role ARN:
   
9) Update your Python runtime to 3.8 or later, as 3.7 is being deprecated.

# Lambda Function Using SAM Template file (CI/CD)
### Here's the script for automatation to create lambda function:
```
#!/bin/bash

set -e  # Exit immediately if a command exits with a non-zero status.

echo "Region: $REGION"
echo "Stack Name: $STACK_NAME"
echo "Bucket Name: $BUCKET_NAME"
echo "Template File: $TEMPLATE_FILE"

if aws lambda get-function --function-name $FUNCTION_NAME &>/dev/null; then 
    echo "Lambda function exists. Updating the function."
    sam deploy --stack-name $STACK_NAME \
               --s3-bucket $BUCKET_NAME \
               --region $REGION \
               --capabilities CAPABILITY_IAM \
               --template-file $TEMPLATE_FILE \
               --no-confirm-changeset \
               --no-fail-on-empty-changeset
else
    echo "Lambda function does not exist, creating now..."
    sam build --template-file $TEMPLATE_FILE
    echo "SAM build completed!"
    
    sam deploy --stack-name $STACK_NAME \
               --s3-bucket $BUCKET_NAME \
               --region $REGION \
               --capabilities CAPABILITY_IAM \
               --template-file $TEMPLATE_FILE \
               --no-confirm-changeset \
               --no-fail-on-empty-changeset
fi

echo "SAM deploy completed!"
```
