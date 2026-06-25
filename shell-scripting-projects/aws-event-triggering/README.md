# AWS S3 to Lambda Notification Example

This project shows a simple AWS flow:
- A file is uploaded to S3
- AWS Lambda runs in response
- Lambda sends a message through SNS

## What is included

- `s3-lambda-function/` - the Lambda code and its Python dependency list
- `s3-lambda-notification-triggering.sh` - a script to create the AWS resources and wire them together
- `example_file.txt` - a sample file uploaded to S3 during setup

## Before you start

Make sure you have:
- AWS CLI configured
- `jq` installed
- `zip` installed
- Python available for installing packages

## How to use

1. Install the Lambda dependency:

```bash
python -m pip install -r s3-lambda-function/requirements.txt
```

2. Run the deployment script:

```bash
cd shell-scripting-projects/aws-event-triggering
bash s3-lambda-notification-triggering.sh
```

## What happens

When the code runs, it:
- creates an S3 bucket and uploads a sample file
- creates a Lambda function
- connects the bucket to Lambda so uploads trigger the function
- creates an SNS topic and sends a notification

## Important

- Update the bucket name and email address in `s3-lambda-notification-triggering.sh` before running.
- Replace the placeholder AWS account ID in `s3-lambda-function.py` with your own if needed.
