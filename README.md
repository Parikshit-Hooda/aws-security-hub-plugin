[![Launch Stack](https://s3.amazonaws.com/cloudformation-examples/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home)

# AWS Security Hub Plugin
## A plug-in to enable integration with AWS Security Hub

### Description
The Aqua Security integration with AWS Security Hub is enabled via a log-forwarder enabler that fetches security events from Aqua and pushes them to the AWS Security Hub.
The log-forwarder component is installed using a CloudFormation script available in GitHub. 
This script deploys the log-forwarded computing instance inside ECS Fargate and connects it with both an Aqua instance and the AWS Security Hub.
The script manages the entire installation process including creating IAM Role with the permission to read and write data from/to the AWS Security Hub and attaches this role to the created instance (task).

### Requirements
- A VPC created
- Existing VPC CIDR range for the new subnet
- AWS Security Hub enabled
- ECS Fargate cluster to run the enabler 

### Before deployment
1. Pull the AWS log-collector image from Aqua's repository and push to  ECR. The CloudFormation template will later push this image to your ECS Fargate cluster:
   - Login to the Aqua Registry with your Aqua credentials: **docker login registry.aquasec.com -u <AQUA_USERNAME> -p <AQUA_PASSWORD>**
   - Pull the AWS log-collector image with the commands: **docker pull aquasec/log-collector:aws-1.4**
   - Push the image to ECR  
2.	Make sure that the Aqua database is available through the standard Postgresql port: 5432

### Deployment: CloudFormation Management Console
1.	Click the Launch Stack icon at the top of this README.md file. This will take you to the Create stack function of the AWS CloudFormation Management Console.
2.  Load the CF template in this repository - secHubEcs.yaml
3.	Ensure that your AWS region is set to where you want to deploy the script.
4.	Click “Next”.
5.	Set or modify any of the parameters below:
    - DockerImage = the path to the log-collector image on ECR 
    - ContainerCpu = CPU "size"; 1024 = 1 full CPU  
    - ContainerMemory = Memory size in megabytes 
    - VPC = ID of the VPC where the script will be deployed 
    - CIDR = CIDR for the newly created subnet inside selected VPC. Must be within selected VPC CIDR range 
    - DBConnectionString = Postgresql URI formated connection string for Aqua's audit DB (e.g. postgresql://{host name or ip}:5432/slk_audit?user=postgres&password={db-password})
    - OutputAWSAccountID = string with AWS account id (for example, "123456789012")
    - ProductARN = string with AWS Product amazon resource name or ARN (i.e "arn:aws:securityhub:<region>:<account-id>:product/<account-id>/default")
    - ECSCluster  = The name of the ECSCluster to host the log-forwarder image
    - LogGroupName = Enter log group name for the log-forwarder
6.	Click “Next” to create the stack.
7.	Acknowledge that the new IAM role will be created automatically while deploying the script
8.	Run the AWS create-stack CLI command.
Deployment time depends on the bootstrap time of the application inside the Docker image.

### Validate deployment
To validate the integration, go to the Aqua Security console and choose to scan a few of the images. The scanning results for these images should be sent as findings to the AWS Security Hub.

### Troubleshooting 
You can check the log-collector logs for errors through the ECS Cluster console view. 
For support issues and questions please contact us at - community.plugins@aquasec.com. 


