

<p align="center">
<a href="https://cloudquery.io">
<img alt="cloudquery logo" width=75% src="https://github.com/cloudquery/cloudquery/raw/main/docs/images/logo.png" />
</a>
</p>

CloudQuery IAM Permissions  
==================================

## Overview:


This solution is designed to help users setup the appropriate roles and permissions in order to use CloudQuery to fetch all of supported resources in their accounts. 

It makes use of [`Service-managed`](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-concepts.html#stacksets-concepts-stackset-permission-models) trust relationships in order to automatically deploy IAM roles into each account in the specified Account List or Organization Unit. The Role that is deployed into each member account is only able to be assumed by the role provisioned in the Admin Account.


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



### Cleaning up:

Run this to delete all resources that were created:

``` bash
aws cloudformation delete-stack --stack-name CloudQueryOrg-Deploy
```


## Links


* Homepage: https://cloudquery.io
* Documentation: https://docs.cloudquery.io
* CloudQuery Hub (providers & policies documentation): https://hub.cloudquery.io/
* Discord: https://cloudquery.io/discord


## Contribution

Feel free to open Pull-Request for small and changes.
