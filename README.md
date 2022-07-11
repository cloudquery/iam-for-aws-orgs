# iam-for-aws-orgs




``` bash
aws cloudformation create-stack --stack-name CloudQueryOrg-Deploy --template-body file://./template.yml  --capabilities CAPABILITY_NAMED_IAM --parameters ParameterKey=OrganizationUnitList,ParameterValue=<ROOT_ORG_ID>
```


``` bash
cloudformation update-stack --stack-name CloudQueryOrg-Deploy --template-body file://./template.yml  --capabilities CAPABILITY_NAMED_IAM --parameters ParameterKey=OrganizationUnitList,ParameterValue=<ROOT_ORG_ID> 
```

``` bash
aws cloudformation delete-stack --stack-name CloudQueryOrg-Deploy
```

TODO: Require Named Session
TODO: Require ExternalId for role assumption in member accounts