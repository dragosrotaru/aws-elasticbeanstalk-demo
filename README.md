# AWS Elastic Beanstalk Docker demo

A minimal walkthrough of deploying a containerised Node.js app to AWS Elastic Beanstalk, using Amazon ECR as the image registry. The app itself is a one-route Express server that returns "Hello World"; the point of the repo is the deployment setup.

> Archived: no longer maintained. Kept for reference.

## What's here

- `src/index.js`: Express server on port 8080
- `Dockerfile`: builds the app on the `node:12` image
- `Dockerrun.aws.json`: Elastic Beanstalk multi-container (v2) definition that pulls the image from ECR and maps host port 80 to container port 8080
- `.ebignore` / `.dockerignore`: keep `node_modules` out of the bundle and image

Replace `<account id>` / `<account-id>` with your AWS account ID. The commands below use `us-east-1`, while `Dockerrun.aws.json` references `us-east-2`; use one region consistently.

## Setup

- Install Node and Docker locally
- Install and configure AWS CLI V2: https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html
- Setup IAM and ECR: https://docs.aws.amazon.com/AmazonECR/latest/userguide/get-set-up-for-amazon-ecr.html

## Push to container registry

1. Login to ECR via Docker `aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <account id>.dkr.ecr.us-east-1.amazonaws.com`
2. Create repository `aws ecr create-repository --repository-name my-test-app --image-scanning-configuration scanOnPush=true --region us-east-1`
3. Build image with `docker build -t my-test-app .`
4. Tag image `docker tag my-test-app:latest <account id>.dkr.ecr.us-east-1.amazonaws.com/my-test-app:latest`
5. Push image `docker push <account id>.dkr.ecr.us-east-1.amazonaws.com/my-test-app:latest`

## Set up Elastic Beanstalk

- Install EB CLI: https://github.com/aws/aws-elastic-beanstalk-cli-setup
- Create project `eb create`. Set load balancer type to network
- Add aws-elasticbeanstalk-ec2-role permissions: https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create_deploy_docker_ecs.html#create_deploy_docker_ecs_role

## Other Docker commands

- View images `docker images`
- Run image `docker run -p 49160:8080 -d my-test-app`
- View running containers `docker ps`
- View container logs `docker logs <container id>`
- Enter container `docker exec -it <container id> /bin/bash`

## Context

Written in September 2020 as a reference for this deployment path. AWS has since retired the multi-container Docker platform on Amazon Linux in favour of ECS-based platforms, and Node 12 is end-of-life, so expect to update both before using it.
