

STEP 1: Login to AWS

Open https://console.aws.amazon.com/

Sign in to your AWS account.

Select region United States (N. Virginia).

Confirm region code is us-east-1.

STEP 2: Create DynamoDB Table

Search for DynamoDB in the AWS search bar.

Open DynamoDB.

Click Tables.

Click Create table.

Enter the following details:

Table name: DateTimeTable2

Partition key: id

Partition key type: String

Sort key: Leave empty

Table settings: Default settings

Click Create table.

Wait until table status becomes Active.

Expected result:

Table Name: DateTimeTable2
Status: Active
Partition Key: id (String)

Note: If DateTimeTable2 already exists, use another unique name such as DateTimeTable3 and update that name in your IAM policy and Python code.

STEP 3: Create AWS Lambda Function

Search for Lambda in AWS.

Open Lambda → Functions.

Click Create function.

Select Author from scratch.

Enter the following:

Function name: StoreDateTime2

Runtime: Python 3.13

Architecture: x86_64

Execution role: Create a new role with basic Lambda permissions (default)

Click Create function.

Expected result:

Function successfully created
Function Name: StoreDateTime2
Runtime: Python 3.13

STEP 4: Open IAM Role From Lambda

Open Lambda → Functions.

Select StoreDateTime2.

Click Configuration.

Click Permissions.

Under Execution role, click the role name.

Example:

StoreDateTime2-role-xxxxx

The IAM page will open.

STEP 5: Create IAM Permission Policy

In IAM, open the role's Permissions tab.

Click Add permissions.

Select Create inline policy.

In the policy editor, select Visual.

Under Select a service, choose DynamoDB.

Select DynamoDB Action

Find Actions allowed.

In Filter Actions, type:

PutItem

Expand Write if needed.

Tick the checkbox next to PutItem.

Keep Effect: Allow.

Do not select All DynamoDB actions.

Specify Resources

Expand Resources.

Select Specific.

Click Add ARNs.

Enter:

Resource in: This account
Resource region: us-east-1
Resource table name: DateTimeTable2
Any region: Unchecked
Any table name: Unchecked

Click Add ARNs.

Confirm the table ARN has been added.

Click Next.

Review and Create Policy

Enter policy name:

DateTimeTable2PutItemPolicy

Check:

Service: DynamoDB
Access level: Limited Write
Resource: DateTimeTable2
Effect: Allow

Click Create policy.

Expected result:

Policy DateTimeTable2PutItemPolicy created.

The inline policy should appear under your Lambda execution role's permissions.

STEP 6: Write Lambda Python Code

Return to AWS Lambda.

Open StoreDateTime2.

Click the Code tab.

Open lambda_function.py.

Delete the default code.

Paste the following code.

Python Code (lambda_function.py)

import json
import boto3
import uuid
from datetime import datetime
from zoneinfo import ZoneInfo

# Connect to DynamoDB
dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('DateTimeTable2')

def lambda_handler(event, context):

    # Get current Indian date and time
    now = datetime.now(ZoneInfo("Asia/Kolkata"))

    # Store date and time in DynamoDB
    table.put_item(
        Item={
            'id': str(uuid.uuid4()),
            'date': now.strftime('%Y-%m-%d'),
            'time': now.strftime('%H:%M:%S')
        }
    )

    # Return success response
    return {
        'statusCode': 200,
        'body': json.dumps('Date and Time stored successfully')
    }

Important: The DynamoDB table name in the code must match the actual table name.

STEP 7: Deploy Lambda Code

After pasting the code, click Deploy.

Wait for the success message.

Expected result:

Successfully updated the function StoreDateTime2.

STEP 8: Create Test Event

In the Lambda Code tab, click Test.

Click Create new test event.

Enter:

Invocation type: Synchronous
Event name: TestDateTime2
Event sharing: Private
Template: hello-world (optional)

Event JSON

Replace the sample JSON with:

{}

Click Save.

Expected result:

Test event TestDateTime2 was successfully saved.

STEP 9: Execute Lambda Function

Select the saved test event TestDateTime2.

Click Test.

Wait for execution results.

Expected output:

{
  "statusCode": 200,
  "body": "\"Date and Time stored successfully\""
}

Expected execution status:

Status: Succeeded

Important: Click Test only once if you want only one record. Each successful test creates a new item with a unique ID.

STEP 10: Verify Data in DynamoDB

Open AWS Console.

Search for DynamoDB.

Click Tables.

Select DateTimeTable2.

Click Explore table items.

Select DateTimeTable2 from the left panel.

Select Scan.

Click Run.
