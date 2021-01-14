# AWS ECR Nodejs Trigger Lambda

This project is based off of [An AWS python script](git@github.com:aws-samples/aws-codecommit-selective-build-trigger.git)

This demo illustrates the deployment of AWS Lambda to build Docker Images of application in AWS CodeCommit repository automatically when selective files in directories (representing the docker image name) are modified and the changes are pushed to the repository. The AWS Lambda receives the AWS CodeCommit events for push to repository and triggers a AWS CodeBuild job to build the Docker image and push to AWS Elastic Container Registry.

AWS services used for the CI/CD portion:

- [AWS CodeCommit](https://aws.amazon.com/codecommit/)
- [AWS CodeBuild](https://aws.amazon.com/codebuild/)
- [AWS CloudFormation](https://aws.amazon.com/cloudformation/)
- [AWS Lambda](https://aws.amazon.com/lambda/)
- [Amazon Elastic Container Registry](https://aws.amazon.com/ecr/)


## Stack deployment

The cloudformation stack can be deployed using Cloudformation page in AWS Console or using the AWS CLI as shown below

First, zip and upload the lambda code to an S3 bucket

`cd src/`

`zip lambda.zip index.js`

`aws s3 cp lambda.zip s3://codecommit-selective-build-ecs-pipeline/`

Trigger the cloudformation stack creation pointing to that S3 bucket zip.

`aws cloudformation create-stack --stack-name <your-stack-name> --template-body file://src/aws-codecommit-selective-build-trigger.yml --capabilities CAPABILITY_NAMED_IAM`

## Components details

[src/aws-ecr-build-trigger.yml](src/aws-ecr-build-trigger.yml) - Cloudformation template for demonstrating the solution of AWS Lambda triggered AWS CodeBuild job based on changes to specific files in AWS CodeCommit repository

[src/index.js](src/index.js) - JavaScript code for AWS Lambda to filter the AWS CodeCommit event and find the files changed as part of the commit and trigger AWS CodeBuild job if needed.

[src/lambda.zip](src/lambda.zip) - Compressed zip file for index.js file for deploying using Cloudformation template

## Codecommit repo setup

In order to deploy multiple images dynamically, the codecommit repository created in the cloud formation stack can be leveraged as follows:

```
/project-root
  service-one
    src
      index.js
    package.json
    Dockerfile
  service-two
    src
      index.js
    package.json
    Dockerfile
  ...
```