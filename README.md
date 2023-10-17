# ECR-ECS-ManualDeploy
Welcome to ECR-ECS-ManualDeploy! Your gateway to understanding the architecture and connection needed to successfully deploy your first Nginx web server on aws ECS. Let's embark on this journey together.

#

## Prerequisites


## Step 2: Create ECS Cluster
 ```sh
aws ecs create-cluster --cluster-name fargate-cluster

 ```

## Step 3: Register a task Definition

```sh
aws ecs register-task-definition --cli-input-json file://task-definition.json

aws ecs list-task-definitions
```

## Step 4: Create a service

```sh

aws ecs create-service --cluster fargate-cluster --service-name fargate-service --task-definition sample-fargate:1 --desired-count 1 --launch-type "FARGATE" --network-configuration "awsvpcConfiguration={subnets=[subnet-0f9f6a75358fe640c],securityGroups=[sg-0aa7a3e98a77d1061],assignPublicIp=ENABLED}"

```
```sh

aws ecs list-services --cluster fargate-cluster

```

## Step 5: Describe the Running Service

```sh
aws ecs describe-services --cluster fargate-cluster --services fargate-service

```

## Test

### get the ARN of the running task
```sh
aws ecs list-tasks --cluster fargate-cluster --service fargate-service
```

### get the ENI ID of the running task

```sh
# Describe the task and locate the ENI ID
aws ecs describe-tasks --cluster fargate-cluster --tasks arn:aws:ecs:us-east-1:147147982092:task/fargate-cluster/4261c7ffc8d142c18739150da3da6525

```

<!-- ENI ID= eni-0585e9037a0a9692d-->

### get the Public IP of the running task 

```sh
#describe the eni to get public  IP and Public Dns Name
aws ec2 describe-network-interfaces --network-interface-id  eni-0585e9037a0a9692d

```

on your browser enter the public IP, Upon success, you should be able to access your App.


## Clean Up

```sh
#Delete Service:
aws ecs delete-service --cluster fargate-cluster --service fargate-service --force
```

```sh
#Delete Cluster:
aws ecs delete-cluster --cluster fargate-cluster
```

# AWS Fargate Task Definition BreakDown:

<!-- # AWS Fargate Task Definition README -->

This README provides an explanation of the AWS Fargate task definition for running a containerized application.

## Task Definition Overview

- **Family**: `sample-fargate`
  - A user-defined name for the task definition.

- **Network Mode**: `awsvpc`
  - Specifies the networking mode for the task, indicating the use of Amazon VPC.

- **Task Role ARN**: `arn:aws:iam::147147982092:role/ECS-taskRole`
  - The IAM role associated with the task, granting specific permissions.

- **Container Definitions**:
  - Defines how the Docker container should run within the task.
  - **Name**: `fargate-app`
    - A name for the container.
  - **Image**: `public.ecr.aws/docker/library/httpd:latest`
    - Docker image, in this case, the latest Apache HTTP server.
  - **Port Mappings**:
    - **Container Port**: `80`
    - **Host Port**: `80`
    - **Protocol**: `tcp`
  - **Essential**: `true`
    - Indicates that this container is essential for the task; task failure if this container fails.
  - **EntryPoint**: `["sh", "-c"]`
    - Command to run when the container starts.
  - **Command**: A command that creates an HTML file and starts the Apache HTTP server with that file as the main content.

- **RequiresCompatibilities**:
  - Specifies the launch type required, in this case, `"FARGATE"`.

- **CPU**: `"256"`
  - The allocated CPU units for the task.

- **Memory**: `"512"`
  - The allocated memory for the task.

## Purpose
This task definition is designed to run a containerized Apache HTTP server serving a simple HTML page. It is configured for AWS Fargate, has specified CPU and memory allocations, and uses an IAM role for permissions. The container listens on port 80, and the task definition is named "sample-fargate."

## Getting Started
You can use this task definition to deploy your containerized applications in AWS Fargate. Be sure to customize the image, CPU, and memory







<!--these instructions are based on this tutorial: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ECS_AWSCLI_Fargate.html-->