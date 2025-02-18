

<p align="center">
<a href="https://cloudquery.io">
<h1 align="center"><img alt="cloudquery logo" width=75% src="https://github.com/cloudquery/cloudquery/raw/main/cli/docs/images/logo.png"/></h1>
</a>
</p>

CloudQuery IAM Permissions  
==================================

## Overview:


This solution is designed to help users setup the appropriate AWS IAM roles and permissions in order to use CloudQuery to fetch all supported resources in their accounts within an AWS Organization. This solution will deploy a child role into each member account and a role in the administrator account for CloudQuery to use.

<p align="center">
  <img width="460"  src="https://user-images.githubusercontent.com/30294676/178352333-7146015f-f8df-4131-953a-d42627458824.png">
</p>

This solution leverages CloudFormation StackSets and [`service-managed`](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-concepts.html#stacksets-concepts-stackset-permission-models) permissions in order to automatically deploy IAM roles into each account in the specified Account List or Organization Unit without additional deployment IAM roles. 

### Organization Management Account or Delegated Administrator Account

For deployment purposes, the template may change depending on where the StackSet is deployed from.  For AWS Organizations, Stacksets can be managed from either the Organization Management (Admin) Account or a [Delegated Administrator Account](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-orgs-delegated-admin.html).  A delegated administrator account is a member account that can create and manage stacksets with service-managed permissions for the organization.  

If using a delegated administrator account, delegated administration must be set up for CloudFormation StackSets.  Follow AWS's guide [here](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-orgs-delegated-admin.html).  For deploying from a delegated administrator account, the `DeployFromDelegatedAdmin` parameter must be set to `true` when deploying the solution.

## Usage


### Deploying this solution:

1. Clone this repo
2. Run the following command but make sure to replace `<ROOT_ORG_ID>` with your OU of the root (if you want to deploy to your entire organization). Or a comma separated list of OUs: 

``` bash
aws cloudformation create-stack --stack-name CloudQueryOrg-Deploy --template-body file://./template.yml  --capabilities CAPABILITY_NAMED_IAM --parameters ParameterKey=OrganizationUnitList,ParameterValue=<ROOT_ORG_ID>
```
3. To get the ARN of role in the Admin and the role deployed in each member account:
``` bash
aws cloudformation describe-stacks --stack-name CloudQueryOrg-Deploy --query "Stacks[].Outputs"
```

4. Using the output you got in step (3) update the following values in your `cloudquery.yml` configuration file:

```
kind: source
spec:
  name: aws
  path: cloudquery/aws
  registry: cloudquery
  version: "v26.6.0" # find latest version here: https://hub.cloudquery.io/plugins/source/cloudquery/aws/latest
  tables: ['aws_s3_buckets']
  destinations: ["postgresql"]
  spec:
    aws_debug: false
    org:
      admin_account:
        role_arn: <AdminRoleArn>
      member_role_name: <MemberRoleName>
    regions: 
      - "*"
```

### Other Parameters:

`AdditionalTrustedArns`: If your deployment pattern is such that the credentials used to run CloudQuery are not in the same management account as where the Stack is deployed, specifying either the AccountID or ARN of the principal that will be running CloudQuery will add in a trust relationship to enable that principal to assume the management role. This is necessary because by default each of the member account roles only has a trust relationship with the single management role that the stack deployed. This feature will enable CloudQuery to assume the role in the management account and then assume the role in each member account.

Here is an example of a Cloudquery configuration file that requires the `AdditionalTrustedArns` parameter:

```
  spec:
    org:
      admin_account:
        role_arn: arn:aws:iam::<MNGMNT_ACCOUNT_ID>:role/cloudquery-mgmt-ro
      member_role_name: cq-ro
      member_trusted_principal:
        role_arn: arn:aws:iam::<MNGMNT_ACCOUNT_ID>:role/cloudquery-mgmt-ro
```



### Cleaning up:

Run this to delete all resources that were created:

``` bash
aws cloudformation delete-stack --stack-name CloudQueryOrg-Deploy
```


## Links


* Homepage: https://cloudquery.io
* Documentation: https://docs.cloudquery.io
* Discord: https://cloudquery.io/discord


## Contribution

Feel free to open Pull-Request for improvements, changes and bug fixes.
