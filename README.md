# iam-for-aws-orgs

## Overview:

<p align="center">
  <img width="460"  src="https://user-images.githubusercontent.com/30294676/178346771-c199a410-f19a-484c-bebc-dfa8e95d8618.png">
</p>

This solution is designed to be deployed in an Admin account in your AWS Organization. It makes use of `[Service-managed](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-concepts.html#stacksets-concepts-stackset-permission-models)` trust relationships in order to automatically deploy IAM roles into each account in the specified Account List or Organization Unit. The Role that is deployed into each member account is only able to be assumed by the role provisioned in the Admin Account.




## Usage


### Deploying this solution:

1. Clone this repo
2. Run the following command but make sure to replace `<ROOT_ORG_ID>` with your OU of the root (if you want to deploy to your entire organization). Or a comma seperated list of OUs: 

``` bash
aws cloudformation create-stack --stack-name CloudQueryOrg-Deploy --template-body file://./template.yml  --capabilities CAPABILITY_NAMED_IAM --parameters ParameterKey=OrganizationUnitList,ParameterValue=<ROOT_ORG_ID>
```
3. To get the ARN of role in the Admin and the role deployed in each member account:
``` bash
aws cloudformation describe-stacks --stack-name CloudQueryOrg-Deploy --region eu-central-1 --query "Stacks[].Outputs"
```



### Cleaning up:

Run this to delete all resources that were created:

``` bash
aws cloudformation delete-stack --stack-name CloudQueryOrg-Deploy
```

TODO: Require Named Session
TODO: Require ExternalId for role assumption in member accounts
