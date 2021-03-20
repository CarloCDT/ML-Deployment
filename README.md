# Deployment of Machine Learning Models

An example of the deployment of a ML model using Flask and Docker in AWS.

## Getting Started

Following these instructions will get you a functioning model on the internet with a given ML model using the following AWS services: EC2, ECR, ECS (AWS Fargate)

### Prerequisites

Have a working ML model with its corresponding `Dockerfile`. In this repository a simple model is given as an example.

### Deploying the ML model with AWS

1. Create a AWS EC2 instance.
  This can be done with the "Launch Instance" button. An Amazon Linux 2 AMI is recommended as it is free tier eligible.
  
2. Update the Python version of the EC2 instance
  For this you can use the command:
  `sudo yum install python3.7 -y`

3. Update the AWS CLI version
  Use the commands:
  `curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"`
  `unzip awscliv2.zip`
  `sudo ./aws/install`
  You may need to restart your SSH connection at this point.

4. Install and start Docker
  This can be done with:
  `sudo yum update -y`
  `sudo amazon-linux-extras install docker -y`
  `sudo service docker start`

5. Give user Docker priviledges
  `sudo usermod -a -G docker ec2-user`
  You may need to restart your SSH connection again at this point. You can check everything is ok with `docker info`.

6. Clone Github repository to EC2
  `sudo yum install git -y`
  `git clone https://github.com/CarloCDT/ML-Deployment.git`
  `cd ML-Deployment`

7. Build Docker image in EC2
  `docker build -t rf-api .`
  You can check the images with 'docker images'.

8. Configure AWS Credentials
  `aws configure`
  In this step you will need your IAM Access key ID and Secret Key. After this create a ECR Repository, and view push commands. 

9. Push Docker container to ECR
  The commands will be similar to these:
  `aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/<given number>`
  `docker tag rf-api:latest public.ecr.aws/<given number>/fargate:latest`
  `docker push public.ecr.aws/<given number>/fargate:latest`

10. Create a ECS Cluster and add a Task Definition with the given image. Select Fargate while creating the cluster.
  This can be done with the "Create Cluster" and "Create new Task Definition" respectively. The image URI can be found in the ECR repository. This will create the public IP like   http://54.242.178.140/apidocs/, from which you can access your model.

## Acknowledgments

The following links were useful to complete this small demo:

- https://docs.aws.amazon.com/cli/latest/userguide/install-linux.html
- https://docs.aws.amazon.com/AmazonECS/latest/userguide/docker-basics.html
