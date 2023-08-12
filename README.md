# aws-service-credentials

Manage key rotation for third party applications that cannot do it themselves.

Let's build:

    - [-] CFTs for deployer framework:
        - [ ] Create S3 Bucket for Terraform STATE
        - [ ] Create Policy to allow deployment
        - [x] Create Group for Deployer
        - [x] Create IAM User for Deployer
        - [x] Generate Access Keys for programmatic deployment
        - [x] Stash credentials in AWS Secrets Manager or SSM Parameter Store
    - [ ] Create Policy
        - change access key
        - AmazonAppStreamReadOnlyAccess
        - SecurityAudit
        - ViewOnlyAccess
        - AmazonMacieFullAccess
        - AWSShieldDRTAccessPolicy
    - [ ] Create Group
    - [ ] Add policies to Group
    - [ ] Create User
    - [ ] Add User to Group
    - [ ] Create AssumeRole that can write to AWS Secrets
    - [ ] Create AssumeRole that can read from AWS Secrets
    - [ ] Create Secret
    - [ ] Stash User Access Key and Secret Access Key in AWS Secret
    - [ ] Retrieve secrets to STDOUT
    - [ ] Rotate secret from CLI
    - [ ] Rotate secret automatically (EventBridge Trigger, ECS task, whatever makes sense)

For practical testing, this will be built around the user required for [prowler](https://github.com/prowler-cloud/prowler) to function.

This policy allows an IAM user to change their own Access Key:
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "iam:DeleteAccessKey",
                "iam:GetAccessKeyLastUsed",
                "iam:UpdateAccessKey",
                "iam:GetUser",
                "iam:CreateAccessKey"
            ],
            "Resource": "arn:aws:iam::918573727633:user/${aws:username}"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": "iam:GetAccountName",
            "Resource": "*"
        }
    ]
}
```


## Assets

`/cfn/` Cloudformation content

    - deployer.yaml (Group, User, and Access Keys for deployment)

## Deployment


### Dependencies

Install the following utilities in your development environment: 
    - aws cli v.2
    - jq
    - python3-pip
    - cfn-lint

Reference commands:

aws cloudformation --profile bio create-stack --stack-name bioDeployer --template-body file://cft/deployer.yaml --capabilities CAPABILITY_NAMED_IAM --tags Key=owner,Value=william.brady
aws cloudformation --profile bio update-stack --stack-name bioDeployer --template-body file://cft/deployer.yaml --capabilities CAPABILITY_NAMED_IAM --tags Key=owner,Value=william.brady
aws cloudformation --profile bio delete-stack --stack-name bioDeployer
