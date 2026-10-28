# AWS Student Registration Application

## Aim
To develop a Student Registration Web Application using AWS Lambda, Amazon API Gateway, Amazon DynamoDB, HTML, and Python.

## Technologies Used
- AWS Lambda
- Amazon DynamoDB
- Amazon API Gateway
- AWS IAM
- Python
- HTML and JavaScript
- Visual Studio Code

## Project Architecture

Student Registration Form (HTML)  
↓  
API Gateway (POST /register)  
↓  
AWS Lambda (Python)  
↓  
Amazon DynamoDB (StudentRegistration)

---

# STEP 1: Login to AWS

1. Open https://console.aws.amazon.com/
2. Sign in to your AWS account.
3. Select the region **US East (N. Virginia) – us-east-1**.
4. Use the same region for DynamoDB, Lambda and API Gateway.

# STEP 2: Create DynamoDB Table

1. Search for **DynamoDB** in AWS Console.
2. Click **Tables**.
3. Click **Create table**.
4. Enter the following:

   - Table name: `StudentRegistration`
   - Partition key: `rollno`
   - Partition key type: String
   - Sort key: Leave empty
   - Table settings: Default

5. Click **Create table**.
6. Wait until the table status becomes **Active**.

Expected result: DynamoDB table `StudentRegistration` created successfully.

# STEP 3: Create AWS Lambda Function

1. Search for **Lambda**.
2. Click **Create function**.
3. Select **Author from scratch**.
4. Enter:

   - Function name: `studentRegistrationFunction`
   - Runtime: Python 3.13 (or another available compatible version)
   - Architecture: x86_64
   - Execution role: Create a new role with basic Lambda permissions

5. Click **Create function**.

Expected result: Lambda function created successfully.

# STEP 4: Give DynamoDB Permissions to Lambda

1. Open `studentRegistrationFunction`.
2. Click **Configuration**.
3. Click **Permissions**.
4. Under Execution role, click the IAM role name.
5. IAM opens.
6. Click **Add permissions → Create inline policy**.
7. Select **Visual**.
8. Select service **DynamoDB**.
9. Under Actions allowed, select **Write → PutItem**.
10. Under Resources, select **Specific**.
11. Click **Add ARNs**.
12. Select **This account**.
13. Resource region: `us-east-1`
14. Resource table name: `StudentRegistration`
15. Click **Add ARNs**.
16. Remove any old ARN containing `YOUR_ACCOUNT_ID`.
17. Click **Next**.
18. Policy name: `StudentRegistrationDynamoDBPolicy`
19. Click **Create policy**.

### Equivalent IAM Policy JSON

If using the JSON editor, replace `YOUR_ACCOUNT_ID` with your actual 12-digit AWS account ID.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "dynamodb:PutItem",
      "Resource": "arn:aws:dynamodb:us-east-1:YOUR_ACCOUNT_ID:table/StudentRegistration"
    }
  ]
}
```

Expected result: Lambda has permission to insert student records into DynamoDB.

# STEP 5: Write Lambda Python Code

1. Return to AWS Lambda.
2. Open `studentRegistrationFunction`.
3. Click **Code**.
4. Open `lambda_function.py`.
5. Delete the existing code.
6. Paste the following code.

```python
import json
import boto3

# Connect to DynamoDB
dynamodb = boto3.resource('dynamodb')

# Select DynamoDB table
table = dynamodb.Table('StudentRegistration')

def lambda_handler(event, context):
    try:
        # Read request body
        body = event.get('body', '{}')

        if isinstance(body, str):
            body = json.loads(body)

        # Get student details
        name = body['name']
        rollno = str(body['rollno'])
        branch = body['branch']

        # Insert data into DynamoDB
        table.put_item(
            Item={
                'rollno': rollno,
                'name': name,
                'branch': branch
            }
        )

        # Return success response
        return {
            'statusCode': 200,
            'headers': {
                'Content-Type': 'application/json'
            },
            'body': json.dumps({
                'message': 'Registration Successful!'
            })
        }

    except Exception as e:
        print("Error:", str(e))

        return {
            'statusCode': 500,
            'headers': {
                'Content-Type': 'application/json'
            },
            'body': json.dumps({
                'message': 'Registration failed'
            })
        }
```

7. Click **Deploy**.

Expected result: Lambda code deployed successfully.

# STEP 6: Test Lambda Function

1. Open the **Test** tab in Lambda.
2. Create a new test event.
3. Event name: `StudentTest`
4. Paste the following JSON:

```json
{
  "body": "{\"name\":\"Test Student\",\"rollno\":\"101\",\"branch\":\"CSE AIML\"}"
}
```

5. Click **Save**.
6. Click **Test**.

### Expected Output

```json
{
  "statusCode": 200,
  "headers": {
    "Content-Type": "application/json"
  },
  "body": "{\"message\": \"Registration Successful!\"}"
}
```

Check DynamoDB to verify the test record was stored.

# STEP 7: Create API Gateway

1. Search for **API Gateway**.
2. Click **Create API**.
3. Select **HTTP API → Build**.
4. Enter API name: `StudentRegistrationAPI`
5. IP address type: IPv4
6. Click **Add integration**.
7. Integration type: Lambda
8. Region: `us-east-1`
9. Select Lambda function: `studentRegistrationFunction`
10. Click **Next**.

## Configure Routes

- Method: `POST`
- Resource path: `/register`
- Integration target: `studentRegistrationFunction`

Click **Next**.

## Define Stages

- Stage name: `$default`
- Auto-deploy: Enabled

Click **Next → Review and create → Create**.

Expected result: HTTP API created successfully.

# STEP 8: Configure CORS

1. Open `StudentRegistrationAPI`.
2. Click **CORS** in the left sidebar.
3. Enter:

   - Access-Control-Allow-Origin: `http://localhost:8000`
   - Access-Control-Allow-Methods: `POST`, `OPTIONS`
   - Access-Control-Allow-Headers: `content-type`
   - Access-Control-Expose-Headers: Leave empty
   - Access-Control-Max-Age: `0`
   - Access-Control-Allow-Credentials: No

4. Click **Add** beside the origin and header fields if required.
5. Click **Save**.

The `$default` stage automatically deploys changes when auto-deploy is enabled.

# STEP 9: Copy API Invoke URL

1. Open API Gateway.
2. Click **Stages**.
3. Select `$default`.
4. Find **Invoke URL**.
5. Copy the URL.

Example:

```text
https://YOUR_API_ID.execute-api.us-east-1.amazonaws.com
```

Add `/register` at the end.

Final endpoint:

```text
https://YOUR_API_ID.execute-api.us-east-1.amazonaws.com/register
```

**Important:** Replace `YOUR_API_ID` with the actual API ID generated by AWS.

# STEP 10: Create HTML Project in VS Code

1. Open VS Code.
2. Click **File → Open Folder**.
3. Create a folder named `StudentRegistration`.
4. Open the folder.
5. Create a new file named `student.html`.
6. Paste the following code.

## student.html

```html
<!DOCTYPE html>
<html>
<head>
    <title>Student Registration</title>
</head>
<body>

    <h1>Student Registration Form</h1>

    <form id="studentForm">

        Name:
        <input type="text" id="name" required>
        <br><br>

        Roll Number:
        <input type="text" id="rollno" required>
        <br><br>

        Branch:
        <input type="text" id="branch" required>
        <br><br>

        <button type="submit">Register</button>

    </form>

    <p id="result"></p>

    <script>

        // STEP 1: Paste API Gateway URL
        const apiUrl =
            "https://YOUR_API_ID.execute-api.us-east-1.amazonaws.com/register";

        // STEP 2: Handle form submission
        document.getElementById("studentForm")
            .addEventListener("submit", async function(event) {

                event.preventDefault();

                // STEP 3: Collect student data
                const studentData = {
                    name: document.getElementById("name").value,
                    rollno: document.getElementById("rollno").value,
                    branch: document.getElementById("branch").value
                };

                // STEP 4: Send data to AWS
                try {

                    const response = await fetch(apiUrl, {
                        method: "POST",
                        headers: {
                            "Content-Type": "application/json"
                        },
                        body: JSON.stringify(studentData)
                    });

                    const result = await response.json();

                    // STEP 5: Display result
                    if (response.ok) {
                        document.getElementById("result").innerText =
                            result.message;
                    } else {
                        document.getElementById("result").innerText =
                            "Registration Failed: " +
                            (result.message || response.status);
                    }

                } catch (error) {

                    document.getElementById("result").innerText =
                        "Error: " + error.message;

                }
            });

    </script>

</body>
</html>
```

7. Replace `YOUR_API_ID` with your actual API Gateway ID.
8. Press **Ctrl + S** to save.

# STEP 11: Run HTML Using Terminal

1. Open VS Code.
2. Open the `StudentRegistration` folder.
3. Click **Terminal → New Terminal**.

## Command 1: Check Python Installation

```powershell
python --version
```

Expected output (version may differ):

```text
Python 3.13.2
```

If `python` is not recognized, try:

```powershell
py --version
```

## Command 2: Start Local HTTP Server

Make sure the terminal is in the folder containing `student.html`.

```powershell
python -m http.server 8000
```

Expected output:

```text
Serving HTTP on :: port 8000 ...
```

If using the Python launcher:

```powershell
py -m http.server 8000
```

Do not close the terminal while testing.

# STEP 12: Open Registration Website

Open Google Chrome.

Enter:

```text
http://localhost:8000/student.html
```

Expected result: Student Registration Form opens.

# STEP 13: Register Student

Enter sample details:

- Name: Arya
- Roll Number: 44
- Branch: CSE AIML

Click **Register**.

### Expected Output

```text
Registration Successful!
```

# STEP 14: Verify Data in DynamoDB

1. Open AWS Console.
2. Search **DynamoDB**.
3. Click **Tables**.
4. Select `StudentRegistration`.
5. Click **Explore table items**.
6. Run the scan if needed.

### Expected Record

```text
rollno: 44
name: Arya
branch: CSE AIML
```

If the record appears, the application is working successfully.

# STEP 15: Important Commands

| Command | Purpose |
|---|---|
| `python --version` | Check Python version |
| `python -m http.server 8000` | Start local server |
| `py -m http.server 8000` | Alternative Windows command |
| `Ctrl + S` | Save code |
| `Ctrl + C` | Stop server |

# STEP 16: Common Errors and Solutions

### Error 1: No module named http.sever

Incorrect:

```powershell
python -m http.sever 8000
```

Correct:

```powershell
python -m http.server 8000
```

### Error 2: AccessDeniedException

Solution:
- Open Lambda → Configuration → Permissions.
- Check the execution role.
- Ensure `dynamodb:PutItem` permission is attached for the correct table.

### Error 3: ResourceNotFoundException

Solution:
- Check table name: `StudentRegistration`
- Check region: `us-east-1`
- Check whether the table exists.

### Error 4: Failed to fetch

Solution:
- Check API Gateway Invoke URL.
- Check CORS settings.
- Check internet connection.
- Confirm the POST route exists.

### Error 5: Registration Failed

Solution:
- Check Lambda CloudWatch logs.
- Check the Lambda code.
- Check IAM permissions.
- Check API Gateway integration.

# STEP 17: Practical Exam Checklist

- [ ] AWS Console opened in us-east-1
- [ ] DynamoDB table created
- [ ] Lambda function created
- [ ] IAM PutItem permission attached
- [ ] Lambda Python code deployed
- [ ] Lambda test successful
- [ ] API Gateway HTTP API created
- [ ] POST /register route configured
- [ ] CORS configured
- [ ] Invoke URL copied
- [ ] HTML file created
- [ ] Python HTTP server started
- [ ] Registration form opened
- [ ] Student registration successful
- [ ] Data verified in DynamoDB

# Final Result

Successfully developed a Student Registration Web Application using HTML, JavaScript, Amazon API Gateway, AWS Lambda, and Amazon DynamoDB.

The application accepts student details through an HTML form and stores them in DynamoDB using a Lambda function.

**Final Workflow:**

HTML Form → API Gateway → AWS Lambda → DynamoDB

**Note:** For a new practical session, update the API Gateway Invoke URL in `student.html`. Delete unused AWS resources after testing to avoid unexpected charges. Never commit AWS access keys or secret keys to GitHub.
