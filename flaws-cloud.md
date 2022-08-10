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
- Check the secret file: http://level2-c8b217a33fcf1f839f6f1f73a00a9ae7.flaws.cloud.s3.amazonaws.com/secret-e4443fc.html