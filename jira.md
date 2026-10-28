Step 1: Create a Jira project
1. Open Jira.
2. Sign in or create an account.
3. Click Projects → Create project (or Spaces → Create space, depending on your Jira version).
4. Select Scrum template.
5. Enter the project name: Student Registration System.
6. Choose a team-managed project if available.
7. Click Create.
Step 2: Create backlog tasks
Go to Backlog → Create issue/work item.

Create these tasks:
Task	Description
Design UI	Create student registration HTML page
Develop Code	Write application code
GitHub Integration	Push project code to GitHub
AWS Deployment	Deploy application on AWS
Testing	Verify application works

Step 3: Create a sprint
1. Open Backlog.
2. Click Create sprint.
3. Move the tasks into the sprint.
4. Click Start sprint.
5. Sprint name: Sprint 1.
6. Duration: 1 week (or the shortest option allowed).
Update the work status as you progress:

To Do → In Progress → Done
Step 4: Create GitHub repository
1. Open GitHub.
2. Click New repository.
3. Repository name: student-registration-agile.
4. Select Public or Private as required.
5. Click Create repository.
Open Git Bash or VS Code terminal and enter:
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/student-registration-agile.git
git push -u origin main


Replace YOUR_USERNAME with your GitHub username.
Step 5: Integrate Jira with GitHub
1. In Jira, open Apps → Explore more apps.
2. Search for GitHub for Atlassian.
3. Install or connect the app.
4. Authorize your GitHub account.
5. Select your GitHub repository.
6. Connect it with Jira.
To link a GitHub commit to a Jira task, include its actual issue key in the commit message:
git add .
git commit -m "SCRUM-1 Create student registration page"
git push origin main


Here, SCRUM-1 is an example. Use the issue key shown in your Jira project.
Step 6: Deploy using AWS
For a simple static HTML application, you can use AWS S3 static website hosting.
1. Open AWS Console.
2. Search for S3.
3. Create a new bucket with a globally unique name.
4. Upload index.html and other website files.
5. Enable static website hosting in bucket Properties.
6. Configure the required public-read permissions and bucket policy for the website files.
7. Open the website endpoint to test it.
Important: S3 static hosting works for frontend files only. If you want to save registration data to DynamoDB, you will also need Lambda and API Gateway. Public S3 hosting makes the website files publicly accessible, so do not upload secrets or personal student records.
Step 7: Complete Jira sprint
1. Return to Jira.
2. Open your active sprint board.
3. Move completed tasks to Done.
4. Verify the GitHub development activity is linked to the correct Jira issues.
5. Click Complete sprint after all required work is finished.
