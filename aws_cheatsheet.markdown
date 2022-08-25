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


