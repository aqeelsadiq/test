This infrastructure contains Terraform configurations for deploying a self-hosted GitHub Actions runner  on AWS. The setup includes an API Gateway, a Lambda function to start EC2 instances, security groups, and a VPC.

#Infrastructure

This project provisions the following AWS resources:

###VPC: 
A custom Virtual Private Cloud with subnets.

###Security Groups: 
Firewall rules to control inbound/outbound traffic.

###EC2 Instances: 
Self-hosted GitHub Actions runners.

###API Gateway: 
A RESTful API endpoint to trigger the Lambda function.

###Lambda Function: 
Starts EC2 instances dynamically based on incoming GitHub webhooks.

###modules Explanation
#GitHub Webhook Setup

The GitHub webhook is configured using the Terraform GitHub provider. The token used for authentication is exported as an environment variable before running Terraform.
export TF_VAR_github_token=<tokenid>
In the API Gateway module (modules/apigateway/main.tf), the webhook is set up to receive workflow-job events from GitHub and forward them to the Lambda function.
