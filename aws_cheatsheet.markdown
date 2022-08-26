<heading>AWS cheatsheet</heading>
---
# PART I: Knowledge base
- How infracture communicate together: token
- Regions > Availability Zones: 26 Regions and 84 Azs
- Threes models: IaaS (Infrastructure as a Service), PaaS (Platform as a Service), SaaS (Software as a Service)
- IAM: Identity and access management: Authentication and Authorization.
---

# PART II.AWS CLI cheatsheet

## I. AWS IAM

### 1. Users
- Get identity: `aws sts get-caller-identity`
- List users: `aws iam list-users`
- List groups for a user: `aws iam list-groups-for-user --user-name user-name` 
- List all [manages policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) that attached to the specified user: `aws iam list-attached-user-policies --user-name user-name`
- List the names of the [inline policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html) embedded in the specificed IAM user: `aws iam list-user-policies --user-name user-name`

### 2. Groups
- List group: `aws iam list-groups`
- List all [manages policies]() that attached to the specified group: `aws iam list-attached-group-policies --group-name groupName`
- List the names of the [inline policies]() embedded in the specified IAM group: `aws iam list-group-policies --group-name groupName`

### 3. Roles
- List of IAM Roles: `aws iam list-roles`
- List all [manages policies]() that attached to the specified role:`aws iam list-attached-role-policies --role-name roleName`
- List the names of the [inline policies]() embedded in the specified IAM role: `aws iam list-role-policies --role-name roleName`

### 4. Policies
- List of IAM Policies: `aws iam list-policies`
- Get policy informarion via policy-arn: `aws iam get-policy --policy-arn policyArn`
- List information about the versions of the speified managed policy: `aws iam list-policy-versions --policy-arn policyArn`
- Get information about the specified versions of the speified managed policy: `aws iam get-policy-version --policy-arn policyArn --version-id versionid`
- Get the specified inline policy document that is embedded on the specified IAM user/group/role:    
   `aws iam get-user-policy --user-name username --policy-name policyname`
   `aws iam get-group-policy --group-name groupname --policy-name policyname`
   `aws iam get-role-policy --role-name groupname --policy-name policyname`

## II. VPC
### 1. 
- Describe about vpcs: `aws ec2 describe-vpcs`
- Describe about Subnets: `aws ec2 describe-subnets`
- Describe about Route Table: `aws ec2 describe-route-tables`
- Describe about Network ACls: `aws ec2 describe-network-acls`
### 2. Pivoting
- Describe all VPC Peering Connections: `aws ec2 describe-vpc-peering-connections`
- Describe about Subnet of the specified VPC: `aws ec2 describe-subnets --filters "Name=vpc-id, values=VPCID"`
- Describe about Route Table of the specified Subnet: `aws ec2 describe-route-tables --filters "Name=vpc-id, values=VPCID"`
- Describe about Network ACL of the specified VPC: `aws ec2 describe-network-acls --filters "Name=vpc-id, values=VPCID"`
- Describe about EC2 instances in the specified VPC: `aws ec2 describe-instances --filters "Name=vpc-id, values=VPCID"`
- Describe about EC2 Instances in specified Subnet: `aws ec2 describe-instances --filters "Name=subnet-id, values=subnetID`

## III. AWS EC2
### 1. 
- Describes the information about all Intances: `aws ec2 describe-instances`
- Des the information about specified instances: `aws ec2 describe-instances --instance-ids instance-ID`
- Des the information about UserData Attribute of sepcified Instance: `aws ec2 describe-instance-attribute --attribute userData --instance-id instance-id`
- Des the information about IAM instance profile associations: `aws ec2 describe-iam-instance-profile-associations`
### 2. GET Credentials
- Get information about attached IAM Role Profile: `aws sts get-caller-identity`
- AWS Metadata:  `http://169.254.169.254/lastest/meta-data/`
- AWS User-data: `http://169.254.169.254/lastest/user-data/`
- More: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instancedata-data-retrieval.html

## IV. AWS EBS
### 1. Enumeration
- Des the information about EBS volumes: `aws ec2 describe-volumes`
- Des the information all the available EBS snapshots: `aws ec2 describe-snapshots --owner-ids self`
### 2. Data Exfiltration: `Level 4 flaws-cloud`
- Step: Create Snapshot from EC2 Instance -> Create Volume from snapshot -> Attached volume
- Des about ec2-instances: `aws ec2 describe-instances`
- Des the information about EBS volumes: `aws ec2 describe-volumes`
- Create a snapshot of the specified volume: `aws ec2 create-snapshot --volume-id VolumeID --description "content"`
- Des about all the available EBS snapshots: `aws ec2 describe-snapshots`
- Create a volume from snapshots: `aws ec2 create-volume --snapshot-id SnapshotID --availability-zone AZ`
- Attached specified volume to the ec2-instance: `aws ec2 attach-volume --volume-id VolumeID --instance-id InstanceID --device /dev/sdfd`
- Mount Volume on EC2 file system: `sudo mount /dev/sdfd /new-dir`

## V. AWS Lambda
### 1. Enumeration `Level 6 flaws-cloud`
**Lambda**
- List of all the lambda functions: `aws lambda list-functions`
- Retrieves the Information about the specified lambda function: `aws lambda get-function --function-name function-name`
- Retrieves the policy about the specified lambda function: `aws lambda get-policy --function-name function-name`
- Retrieves the event source mapping Information about the specified lambda fnction: `aws lambda list-event-source-mappings --function-name function-name`
- List of all the layers (dependencies) in aws account: `aws lambda list-layers`
- Retrives the full informarion about the specified layer name: `aws lambda get-layer-version --layer-name Layername --version-number VersioNumber`

**API Gateway**
- List of all the Rest APIs: `aws apigateway get-rest-apis`
- Get the information about specified API: `aws apigateway get-rest-api --rest-api-id APIId`
- Lists information about a collection of resources: `aws apigateway get-resources --rest-api-id APIId`
- Get information about the specified resource: `aws apigateway get-resources --rest-api-id APIId --resource-id ResourceID`
- Get the method information for the specified resource: `aws apigateway get-method --rest-api-id ApiID --resource-id ResourceID --http-method Method`
- List of all stages for a REST API: `aws apigateway get-stages --rest-api-id ApiId`
- Get the information about specified API's stage: `aws apigateway get-stage --rest-api-id ApiId --stage-name StateName`
- List of all the API keys: `aws apigateway get-api-keys --include-values`
- Get the information about a sepcified API key: `aws apigateway get-api-key --api-key ApiKey`

## VI. AWS S3
### 1. Enumeration
- List of all the buckets in the aws account: `aws s3api list-buckets`, `aws s3 ls`
- Get the information about specified bukcet acls: `aws s3api get-bucket-acl --bucket bucketname`
- Get the information about specified bucket policy: `aws s3api get-bucket-policy --bucket bucketname`
- Retrieves the Public Access Block congiuration for an AWS S3: `aws s3api get-public-access-block --bucket bucketname`
- List of all the Objects in specified bucket: `aws s3api list-objects --bucket bucketname`, `aws s3 ls s3://bucketname`
- Get the acls infor about specified object: `aws s3api get-object-acl --bucket bucketname --key object-name`
- Retrieves the Public Access Block configuration for an S3 bucket: `aws s3api get-object --bucket bucketname --key object-name download-file-location`, `aws s3 cp s3:/bucketname/object download-file-localtion`, `aws s3 sync s3://bucketname location`
- Time Based URL - resign: `aws s3 presign s3://bucketname/object --exprires-in 604800`
---
# PART III: Testing for AWS

## A. Your AWS cloud external infrastructure

### I. Public resources to external: S3Bucket, eb, repo, iaac build...: read, write priv?

Recon?
- Scan s3 subdomain via regions format: 
   - S3 BUCKET: 's3.`region`.amazonaws.com', <s3_craler.py>, nuclei detect.
   - Elastic beanstalk: `region`.elasticbeanstalk.com 
   - Git commit: http://git-codecommit.`region`.amazonaws.com/v1/repos/<name>: rare
   - EC2 credentials, elb: rare
   - Cloudfront.
   - RDS
   - AWS CloudTrail.
- Search via gitlab, github, cs.github.com, postman, google,...: spiderfoot, google dork, shodan,...
- https://duo.com/decipher/exposed-aws-resources-leaked-sensitive-data

### II. Public credetials in external storage: 
- Github, gitlab, Postman, AWS Access Key ID, Secret key, aws_key, aws_secret...
- Enumerate IAM account, weak password policy...

-> Misconfigure
-> Using recon tool
-> https://portal.intruder.io/

## B. Your hosting/building platform applications

- Testing for Elastic beanstalk application
- Pentest app
- EC2 instance 
- Is pipeline, github, gitlab action vulnerables to RCE, key leak?
- Becarefull with SSRF

## C. Your AWS cloud internal infrastructure

 - IAM rule
 - Security Groups
 - Is port public, Inbound, outbound rules.
 - VPC

## D. Configuration review of your AWS setup/environment?
- AWS config review
- AWS Inspect


