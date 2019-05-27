# Cloudformation template - SPA on S3 with Lambda + RDS as API

## Available Parameters
|Key|Type|Default|
|--|--|--|
| S3BucketName | String | - |
| GitHubRepo | String | https://github.com/aleksandar-babic/vue-s3-demo |
| CodeBuildProjectName | String | - |
| CodeBuildComputeType | String | BUILD_GENERAL1_SMALL |
| CodeBuildEnvImage | String | node:alpine |
| CodeBuildTimeout | Number | 10 |
| VueBaseApiUrl | String | https://jsonplaceholder.typicode.com |


## How to deploy this template
- Clone this repo
```bash
git clone git@github.com:aleksandar-babic/aws-cf-s3-rds.git && cd aws-cf-s3-rds
```
- Copy Github credentials json in `source-credentials.json`
```bash
cp source-credentials.example.json source-credentials.json
```
- Change username and token (If you don't have personal token, create it [here](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line)) in `source-credentials.json`
-  Import source credentials from JSON into your account
```bash
aws codebuild import-source-credentials --cli-input-json file://source-credentials.json
```
- Deploy Cloudformation template with
```bash
aws cloudformation deploy --template-file cf-s3-codebuild-lambda-rds.yml --capabilities \
CAPABILITY_NAMED_IAM --parameter-overrides S3BucketName=bucket-name-here \
CodeBuildProjectName=codebuild-project-name-here --stack-name stack-name-here
```
> Only prerequirement for this template is VPC with IGW
