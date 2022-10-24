# RMIT Serverless ChatBot
Github url: https://github.com/MatthewWoolley-S3908867/RMITChatbotCapstone

deployed at: https://d33hwz5qdc8qs4.cloudfront.net/

username: test

password LKfWO&m0

## On this Page

- [Architecture Overview](#architecture-overview)
- [Deployment](#deployment)
- [Source Code](#source-code)

## Architecture Overview

![Architecture](deployment/architecture.png)

## Deployment

The solution is deployed using a CloudFormation template with a lambda backed custom resource. For details on deploying the solution please see the details on the solution home page: [Serverless Bot Framework](https://aws.amazon.com/solutions/serverless-bot-framework/) with certain changes done to make the code funciton, check the changes folder for a txt with these edits 

Note: these changes do include python changes that have not been indented, if you need to update the previous framework, make sure to indent them correctly

## Source Code

**source/infrastructure**
Includes AWS CDK code describing the architecture of the solution. This CDK code will be transpiled into Cloudformation template when running `build-s3-dist.sh`.

**source/samples**
Includes three components, a lambda function that integrates with Amazon Lex, a custom resource, and a front-end web client. This code can be extended to add different functionalities to the bot.

**source/services**
Includes source code for three lambda functions, core, lex-bot, webclient-setup, and polly-service.

**source/services/solution-helper**
A Python Lambda function used as a CloudFormation custom resource for configuring Amazon S3 bucket notifications and to send anonymous metrics.

## Database
Check the database.txt file in this repo/folder to learn about the current database set up

## Deploying our custom build

The solution can be deployed through the CloudFormation template available on the solution home page: [Serverless Bot Framework](https://aws.amazon.com/solutions/implementations/serverless-bot-framework/).
To make changes to the solution, download or clone this repo, update the source code and then run the deployment/build-s3-dist.sh script to deploy the updated Lambda code to an Amazon S3 bucket in your account.




### Prerequisites:

- [AWS Command Line Interface](https://aws.amazon.com/cli/)
- Node.js 12.x or later
- Python 3.8 or later
- [AWS CDK](https://aws.amazon.com/cdk/)

### 1. Create the deployment packages

Build the distributable:

```
ARTIFACT_BUCKET=my-bucket-name     # S3 bucket name where customized code will reside
SOLUTION_NAME=my-solution-name     # customized solution name
VERSION=my-version                 # version number for the customized code

cd ./deployment
chmod +x ./build-s3-dist.sh
./build-s3-dist.sh $ARTIFACT_BUCKET $SOLUTION_NAME $VERSION
```

> **Notes**: The _build-s3-dist_ script expects the bucket name as one of its parameters, and this value should not include the region suffix.

### 2. Create Amazon S3 Buckets

The CloudFormation template is configured to pull the Lambda deployment packages from Amazon S3 buckets in the region the template is being launched in. You need to create two buckets in the desired region. The names of the buckets should be `$ARTIFACT_BUCKET` and `$ARTIFACT_BUCKET-<region-name>`. For example, the following will create two buckets in the us-east-1 region.

```
aws s3 mb s3://$ARTIFACT_BUCKET --region us-east-1
aws s3 mb s3://$ARTIFACT_BUCKET-us-east-1 --region us-east-1
```

Deploy the distributable to the Amazon S3 bucket in your account:

```
aws s3 sync ./global-s3-assets s3://$ARTIFACT_BUCKET/$SOLUTION_NAME/$VERSION --acl bucket-owner-full-control
aws s3 sync ./regional-s3-assets s3://$ARTIFACT_BUCKET-us-east-1/$SOLUTION_NAME/$VERSION --acl bucket-owner-full-control
```

### 3. Launch the CloudFormation template.

- Get the link of the serverless-bot-framework.template uploaded to your Amazon S3 bucket.
- Deploy the serverless bot framework to your account by launching a new AWS CloudFormation stack using the link of the serverless-bot-framework.template.

### 4. Import our custom Lambda Functions into the solution

- Head to the AWS Lambda service and select Bot-LexLambdaDynamoDBLambdaFunction and Bot-coreLambdaLambdaFunction
- Scroll to Code source and upload from ZIP. Upload our ZIP file containing our code changes. Do this for both functions.

###. 5.Import Joe bot into Lex

-  Head to lex and import the JSON bot existing at the root of the git repo name it joe, then make sure to go into allias as connect the lambda to joe in order for it to work


## Known Behavior

Pressing enter does not submit a message.          
