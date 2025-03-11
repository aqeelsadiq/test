This infrastructure contains Terraform configurations for deploying a self-hosted GitHub Actions runner  on AWS. The setup includes an API Gateway, a Lambda function to start EC2 instances, security groups, and a VPC.

# Infrastructure

This project provisions the following AWS resources:

1. VPC: 
A custom Virtual Private Cloud with subnets.

2. Security Groups: 
Firewall rules to control inbound/outbound traffic.

3. EC2 Instances: 
Self-hosted GitHub Actions runners.

4. API Gateway: 
A RESTful API endpoint to trigger the Lambda function.

5. Lambda Function: 
Starts EC2 instances dynamically based on incoming GitHub webhooks.

# modules Explanation
# GitHub Webhook Setup

The GitHub webhook is configured using the Terraform GitHub provider. The token used for authentication is exported as an environment variable before running Terraform.

export TF_VAR_github_token="tokenid"

In the API Gateway module (modules/apigateway/main.tf), the webhook is set up to receive workflow-job events from GitHub and forward them to the Lambda function.

# EC2 Instance Configuration

In the EC2 module (modules/ec2/user_data.sh), the instance retrieves the GitHub Personal Access Token (PAT) and GitHub Owner details manually set in AWS Systems Manager SSM Parameter Store. The script fetches these values using the AWS CLI.

ec2 instances are in the private subnets.

"aws ssm get-parameter --name "/lambda/github-owner" --with-decryption --query "Parameter.Value" --output text --region us-west-1"

# Lambda Function Configuration

The Lambda module fetches the required GitHub credentials using a Terraform data block. These values are stored as environment variables in the Lambda function configuration:

GITHUB_TOKEN: Retrieved from Terraform.

GITHUB_OWNER: Retrieved from Terraform.

REPO_NAME: Retrieved from Terraform.

The Lambda function then uses these environment variables to interact with the GitHub API and determine which EC2 instance to start.

# app
in the app dorectory i use the lambda function code lambda_function.py and path is given in the module/lambda/main.tf

# Deployment steps

1. aws account and credentials configured
    **aws configure**

2. Terraform installed

3. Githubactions setup to use self-hosted runners

4. initialize terraform
    **terraform init**

5. plan terraform 
    **terraform plan -var-file=terraform.tfvars  # use the file name because export TF_VAR to read the variable**

6. Apply terraform
    **terraform apply --auto-approve -var-file=terraform.tfvars**

