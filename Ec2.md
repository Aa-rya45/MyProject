Practical 7: Install Terraform and Provision EC2 Instance on AWS

Aim

To install Terraform and use Terraform configuration files to create an Amazon EC2 instance on AWS.

Requirements

- Windows 10/11
- PowerShell
- AWS Account
- Terraform
- AWS CLI
- Internet Connection

Step 1: Login to AWS Console

1. Open https://console.aws.amazon.com/
2. Sign in to your AWS account.
3. Search for EC2.
4. Select region Asia Pacific (Mumbai).
5. Region code: "ap-south-1"

Step 2: Install Terraform

1. Open https://developer.hashicorp.com/terraform/install
2. Download Terraform for Windows (AMD64).
3. Extract the downloaded ZIP file.
4. Create a folder "C:\Terraform".
5. Copy "terraform.exe" into "C:\Terraform".
6. Search Windows for Edit the system environment variables.
7. Click Environment Variables.
8. Under Path, click Edit → New.
9. Add "C:\Terraform".
10. Click OK and reopen PowerShell.

Command

terraform -version

Expected output: Displays the installed Terraform version.

Step 3: Install AWS CLI

1. Open https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
2. Download AWS CLI for Windows.
3. Install it.
4. Close and reopen PowerShell.

Command

aws --version

Expected output: Displays the installed AWS CLI version.

Step 4: Configure AWS Credentials

Create or obtain IAM credentials with the required EC2 permissions. Do not create root-user access keys.

Open PowerShell.

Command

aws configure

Enter the following:

AWS Access Key ID: YOUR_ACCESS_KEY_ID
AWS Secret Access Key: YOUR_SECRET_ACCESS_KEY
Default region name: ap-south-1
Default output format: json

Replace the placeholders with your own IAM credentials.

Verify AWS Connection

aws sts get-caller-identity

Expected output: AWS account ID, user ID, and ARN.

Note: Never upload actual AWS access keys to GitHub.

Step 5: Create Terraform Project

Command 1: Create project folder

mkdir terraform-ec2

Command 2: Enter project folder

cd terraform-ec2

Command 3: Create Terraform file

notepad main.tf

- Notepad will open.
- Click Yes if asked to create a new file.
- Paste the Terraform code given below.
- Press "Ctrl + S" to save.
- Close Notepad.

Step 6: Terraform Configuration Code

File Name: "main.tf"

terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}

provider "aws" {
  region = "ap-south-1"
}

# Find an Ubuntu 24.04 AMI in the selected region
data "aws_ami" "ubuntu" {
  most_recent = true
  owners      = ["099720109477"]

  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd-gp3/ubuntu-noble-24.04-amd64-server-*"]
  }

  filter {
    name   = "architecture"
    values = ["x86_64"]
  }

  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }
}

# Create an EC2 instance
resource "aws_instance" "web_server" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t3.micro"

  tags = {
    Name = "Terraform-EC2"
  }
}

# Display the public IP address
output "instance_public_ip" {
  value = aws_instance.web_server.public_ip
}

Explanation:

- "terraform": Defines the required provider.
- "provider "aws"": Connects Terraform to AWS.
- "region": Specifies Mumbai.
- "data "aws_ami"": Finds an Ubuntu machine image.
- "resource "aws_instance"": Creates an EC2 instance.
- "instance_type": Defines the virtual machine size.
- "tags": Gives the instance a name.
- "output": Displays the public IP address, if available.

Important: "t3.micro" may incur AWS charges.

Step 7: Initialize Terraform

Command

terraform init

Purpose:

- Downloads the AWS provider.
- Initializes the Terraform working directory.
- Prepares Terraform to manage AWS resources.

Expected Output

Terraform has been successfully initialized!

Step 8: Validate Configuration

Command

terraform validate

Purpose: Checks whether the Terraform configuration is valid.

Expected Output

Success! The configuration is valid.

Step 9: Preview Changes

Command

terraform plan

Purpose: Shows the resources Terraform plans to create.

Expected Output

Plan: 1 to add, 0 to change, 0 to destroy.

Meaning: Terraform plans to create one EC2 instance.

Step 10: Create EC2 Instance

Command

terraform apply

Terraform will ask for confirmation.

Do you want to perform these actions?
Enter a value:

Type

yes

Press Enter.

Expected Output

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

The EC2 instance is now created on AWS.

Step 11: Verify EC2 Instance in AWS Console

1. Open https://console.aws.amazon.com/
2. Search for EC2.
3. Select Mumbai (ap-south-1).
4. Click Instances.
5. Find "Terraform-EC2".
6. Check the instance state.
7. Wait until the instance shows Running.

Display Public IP

In PowerShell:

terraform output instance_public_ip

Expected output: The EC2 instance's public IP address, if assigned.

Step 12: Verify Using AWS CLI

Command

aws ec2 describe-instances --filters "Name=tag:Name,Values=Terraform-EC2" --query "Reservations[].Instances[].[InstanceId,State.Name,PublicIpAddress]" --output table --region ap-south-1

Expected output: Displays the instance ID, instance state, and public IP address.

Step 13: Destroy EC2 Instance After Practical

Important: After showing the practical to the examiner, delete the EC2 instance to avoid unnecessary charges.

Command

terraform destroy

When Terraform asks for confirmation, type:

yes

Expected Output

Destroy complete! Resources: 1 destroyed.

The Terraform-managed EC2 instance is deleted.

---

All Commands in Sequence

# Step 1: Check Terraform installation
terraform -version

# Step 2: Check AWS CLI installation
aws --version

# Step 3: Configure AWS credentials
aws configure

# Step 4: Verify AWS connection
aws sts get-caller-identity

# Step 5: Create project folder
mkdir terraform-ec2

# Step 6: Enter project folder
cd terraform-ec2

# Step 7: Create main.tf file
notepad main.tf

# Paste Terraform code, save and close Notepad

# Step 8: Initialize Terraform
terraform init

# Step 9: Validate Terraform configuration
terraform validate

# Step 10: Preview changes
terraform plan

# Step 11: Create EC2 instance
terraform apply

# Type yes and press Enter

# Step 12: Display public IP
terraform output instance_public_ip

# Step 13: Delete EC2 instance after verification
terraform destroy

# Type yes and press Enter

Result

Successfully installed Terraform, configured AWS credentials, created an Amazon EC2 instance using Terraform, and verified the instance in AWS Management Console.

Viva Questions

1. What is Terraform?

Terraform is an Infrastructure as Code tool used to create and manage cloud resources.

2. What is Infrastructure as Code (IaC)?

IaC means managing infrastructure using code instead of manually.

3. What is Amazon EC2?

EC2 is an AWS service that provides virtual servers.

4. What is a Terraform provider?

A provider allows Terraform to communicate with cloud platforms such as AWS.

5. What is "main.tf"?

It is a Terraform configuration file.

6. What is "terraform init"?

It initializes the Terraform project.

7. What is "terraform validate"?

It checks whether the configuration is valid.

8. What is "terraform plan"?

It previews the changes Terraform will make.

9. What is "terraform apply"?

It creates or updates the cloud resources.

10. What is "terraform destroy"?

It deletes the resources managed by Terraform.

11. What is an AMI?

An Amazon Machine Image is a template used to launch an EC2 instance.

12. What is the purpose of AWS CLI?

AWS CLI allows us to manage AWS services using terminal commands.
