

<p align="center">
<a href="https://cloudquery.io">
<h1 align="center"><img alt="cloudquery logo" width=75% src="https://github.com/cloudquery/cloudquery/raw/main/cli/docs/images/logo.png"/></h1>
</a>
</p>

CloudQuery IAM Permissions  
==================================

## Overview:


This solution is designed to help users setup the appropriate roles and permissions in order to use CloudQuery to fetch all of supported resources in their accounts. 

It makes use of [`Service-managed`](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-concepts.html#stacksets-concepts-stackset-permission-models) trust relationships in order to automatically deploy IAM roles into each account in the specified Account List or Organization Unit. The Role that is deployed into each member account is only able to be assumed by the role provisioned in the Admin Account.

Note: For deployment purposes, the template may change depending on where the StackSet is deployed from.  For AWS Organizations, Stacksets can be managed from either the Organization Management (Admin) Account or a [Delegated Administrator Account](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-orgs-delegated-admin.html).  A delegated administrator account is a member account that can create and manage stacksets with service-managed permissions for the organization.  

For deploying from a delegated administrator account, `DELEGATED_ADMIN` must be specified in the `CallAs` property in the [CloudFormation StackSet](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-cloudformation-stackset.html#aws-resource-cloudformation-stackset-properties).

<p align="center">
  <img width="460"  src="https://user-images.githubusercontent.com/30294676/178352333-7146015f-f8df-4131-953a-d42627458824.png">
</p>


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
providers:
    # provider configurations
    - name: aws
      configuration:
        org:
          admin_account:
            role_arn: <AdminRoleArn>
          member_role_name: <MemberRoleName>
        regions: 
          - "*"
      # list of resources to fetch
      resources:
        - "*"
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
