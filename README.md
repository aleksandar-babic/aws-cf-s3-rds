# Cloudformation template - SPA on S3 with ApiGateway + Lambda + RDS as API

## Working example

Below are example (they are all working, check them out!) Outputs from this template:

- S3BucketURL: http://agt-s3-vue.s3-website.eu-central-1.amazonaws.com
- S3BucketSecureURL: https://agt-s3-vue.s3.amazonaws.com/index.html
- ApiGatewayInvokeURL: https://z4tkeel9nk.execute-api.eu-central-1.amazonaws.com/v1
- RDSHostname: ar1xhfp6ybmx6j9.cush8cosudbc.eu-central-1.rds.amazonaws.com:3306/AGTtest

Explanation:

- `S3BucketURL` and `S3BucketSecureURL` represent URLs(http and https) to VueJS App(built by AWS CodeBuild on every Github commit) served from S3.
- `ApiGatewayInvokeURL` represents URL to Api Gateway Method that will Execute Python Lambda which fetches data from RDS.
- `RDSHostname` represents connection string for RDS (RDS is not publicly available).

## AWS Cloudformation Diagram

![aws-cf-diagram](https://i.gyazo.com/eea0b51255f3779f222118e0493a6e5b.png)

## Available Parameters
|Key|Type|Default|
|--|--|--|
| S3BucketName | String | - |
| GitHubRepo | String | https://github.com/aleksandar-babic/vue-s3-demo |
| CodeBuildProjectName | String | - |
| CodeBuildComputeType | String | BUILD_GENERAL1_SMALL |
| CodeBuildEnvImage | String | node:alpine |
| CodeBuildTimeout | Number | 10 |
| ApiGatewayStageName | String | v1 |
| LambdaFunctionName | String | fetch-data-function |
| LambdaMemoryLimit | Number | 128 |
| LambdaRuntime | String | python2.7 |
| LambdaTimeout | Number | 10 |
| LambdaSubnets | List<AWS::EC2::Subnet::Id> | 10 |
| LambdaS3Bucket | String | rds-lambda-test-agt |
| LambdaS3Key | String | fetch-data-fn-deployment.zip |
| VpcId | AWS::EC2::VPC::Id | - |
| DBName | String | AGTtest |
| DBUser | String | - |
| DBPassword | String | - |
| DBInstanceClass | String | db.t2.small |
| DBEngine | String | mysql |
| DBEngineVersion | String | 5.6.40 |
| RDSParamGroupFamily | String | MySQL5.6 |
| RDSSGCidr | String | 0.0.0.0/0 |

## Outputs

|Key|Description|
|--|--|
| S3BucketURL | URL for SPA hosted on S3 |
| S3BucketSecureURL | Secure URL for SPA hosted on S3 |
| ApiGatewayInvokeURL | URL for API Gateway resource that will return RDS data |
| RDSHostname | RDS DB internal hostname and port |

# Resources created by this template

|Logical Name|Type|
|--|--|
| ApiGateway | AWS::ApiGateway::RestApi	 |
| ApiGatewayDeployment	 | AWS::ApiGateway::Deployment	 |
| ApiGatewayMethod | AWS::ApiGateway::Method	 |
| BucketPolicy | AWS::S3::BucketPolicy	 |
| CodeBuildProject | AWS::CodeBuild::Project	 |
| CodeBuildServiceRole	 | AWS::IAM::Role	 |
| LambdaApiGatewayInvoke | AWS::Lambda::Permission	 |
| LambdaFunction | AWS::Lambda::Function	 |
| LambdaIAMRole | AWS::IAM::Role	 |
| LambdaLogGroup	 |  AWS::Logs::LogGroup	|
| RDS |  AWS::RDS::DBInstance	|
| RDSParamGroup	 | AWS::RDS::DBParameterGroup	 |
| RDSSecurityGroup	 | AWS::EC2::SecurityGroup	 |
| S3Bucket	 |AWS::S3::Bucket	|

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
CodeBuildProjectName=codebuild-project-name-here DBUser=db-user-here DBPassword=db-password-here VpcId=vpc-id-here RDSSGCidr=cidr-here --stack-name stack-name-here
```
- Or apply template from AWS Console

- Run initial CodeBuild build (any commit will also trigger build)

> Only prerequirement for this template is VPC with IGW

> This example uses hardcoded Lambda function written in Python that connects to RDS and fetches todos from the DB table.
> Deployment package of Python Lambda function can be downloaded [here](https://s3.eu-central-1.amazonaws.com/rds-lambda-test-agt/fetch-data-fn-deployment.zip).
