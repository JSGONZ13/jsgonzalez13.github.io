---
layout: page
title: About
date:   2024-06-22 18:15:00 -0500
categories: jekyll update
---

# Deploying a Python Lambda Function

## Prerequisites
Before deploying a Python Lambda function, ensure you have the following:
- AWS account
- AWS CLI installed and configured
- Python installed
- Basic knowledge of Python and AWS Lambda

## Step-by-Step Guide

### Step 1: Write Your Lambda Function
Create a Python script for your Lambda function. For example, create a file named `lambda_function.py`:

```python
def lambda_handler(event, context):
    return {
        'statusCode': 200,
        'body': 'Hello from Lambda!'
    }
```

### Step 2: Create a Deployment Package
Lambda functions require a deployment package containing your function code and dependencies. If your function does not have any dependencies, you can simply zip the file:

```sh
zip function.zip lambda_function.py
```

If you have dependencies, you need to include them in the package. First, create a virtual environment and install dependencies:

```sh
python -m venv venv
source venv/bin/activate
pip install requests  # Example dependency
deactivate
```

Next, package the dependencies with your function code:

```sh
cd venv/lib/python3.8/site-packages
zip -r9 ../../../../function.zip .
cd ../../../../
zip -g function.zip lambda_function.py
```

### Step 3: Create an IAM Role
Create an IAM role that the Lambda function will use. This role needs permissions to execute the function and log to CloudWatch. Use the AWS Management Console or AWS CLI:

```sh
aws iam create-role --role-name lambda-ex --assume-role-policy-document file://trust-policy.json
```

Create a `trust-policy.json` file with the following content:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "lambda.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

Attach the AWSLambdaBasicExecutionRole policy to the role:

```sh
aws iam attach-role-policy --role-name lambda-ex --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole
```

### Step 4: Deploy the Lambda Function
Deploy the function using the AWS CLI:

```sh
aws lambda create-function     --function-name my-function     --zip-file fileb://function.zip     --handler lambda_function.lambda_handler     --runtime python3.8     --role arn:aws:iam::YOUR_ACCOUNT_ID:role/lambda-ex
```

### Step 5: Test the Lambda Function
Invoke the function using the AWS CLI to test it:

```sh
aws lambda invoke --function-name my-function output.txt
cat output.txt
```

You should see a response similar to:

```json
{
  "statusCode": 200,
  "body": "Hello from Lambda!"
}
```

### Step 6: Update the Lambda Function
If you need to update your function, upload the new zip file:

```sh
aws lambda update-function-code --function-name my-function --zip-file fileb://function.zip
```

### Step 7: Clean Up
If you no longer need the Lambda function, delete it:

```sh
aws lambda delete-function --function-name my-function
```

Also, remove the IAM role:

```sh
aws iam delete-role --role-name lambda-ex
```

## Conclusion
Deploying a Python Lambda function involves writing your function code, creating a deployment package, setting up necessary IAM roles, and using the AWS CLI to deploy and manage your function. With these steps, you can efficiently deploy and update your Python Lambda functions on AWS.
