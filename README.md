# epam-aws-s3-deploy-static-website
This is a simple practice on how to use github actions ci_cd to deploy a simple static website in aws s3

## Steps
1. Create bucket using AWS S3 service.
2. Make bucket publicly readable and edit bucket resource based policy to allow any principal to read the objects in the bucket
~~~
    {
        "Version": "2012-10-17",
        "Statement": [
          {
              "Sid": "Statement1",
              "Effect": "Allow",
              "Principal": "*",
              "Action": "s3:GetObject",
              "Resource": "arn:aws:s3:::danny-herrera-4637/*"
          }
        ]
    }
~~~
3. In your bucket, under properties configure static web hosting and specify the entry point files for init page (mandatory) and error page (optional)

<p align="center">
  <img src="https://github.com/user-attachments/assets/c9781168-734f-4fc9-9803-012779b71418" alt="Description of image" width="1200">
</p>

5. Following AWS security best practices, we configure IAM role to permit the github workflow to upload files into the newly created bucket without the need to store long term user credentials as github secrets. We use AWS IAM to configure identity provider by using OpenID Connect (OIDC), this is for AWS to trust github's OIDC federated identity. For details see https://docs.github.com/en/actions/security-for-github-actions/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services

<p align="center">
  <img src="https://github.com/user-attachments/assets/61a5eae7-ce85-4f41-aebd-3723f46b1542" alt="Description of image" width="1200" height="200">
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/9bf998db-1b96-4e62-9a0f-53e26d9a779f" alt="Description of image" width="1200">
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/7484bec4-c62a-43a9-bdeb-c3cfcbebbb98" alt="Description of image" width="1200" height="200">
</p>

4. Using AWS IAM, we create a new custom IAM role and configure the trust policy shown below. This trust policy is to specify who can assume the role to interact with our AWS resources; also for this role it's important to consider another two things: first, limit what the role can do, this can be achieved by attaching one or multiple IAM policy entities to the role, for instace attaching a custom S3 upload only policy and second, specify with a condition statement who can assume the role using the github's OIDC. There are some placeholders that will be needed to be change according to the account <AWS_ACCOUNT_ID>, <REPO_OWNER>, <REPO_NAME> and <BRANCH_NAME> with your data. 
~~~
#IAM role Trust Policy
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Federated": "arn:aws:iam::<AWS_ACCOUNT_ID>:oidc-provider/token.actions.githubusercontent.com" #Who can assume the role
            },
            "Action": "sts:AssumeRoleWithWebIdentity",
            "Condition": { #Specify conditions of when the role can be assumed
                "StringEquals": {
                    "token.actions.githubusercontent.com:aud": "sts.amazonaws.com",
                    "token.actions.githubusercontent.com:sub": "repo:<REPO_OWNER>/<REPO_NAME>:refs/heads/<REPO_BRANCH>"
                }
            }
        }
    ]
}
~~~

<p align="center">
  <img src="https://github.com/user-attachments/assets/71f3fe9a-7357-43a2-a31f-b0b6c9f703e8" alt="Description of image" width="1200">
</p>

5. We Configure github workflow to upload static website files to s3 bucket on git push event (See code in .gitub/workflows/deploy-static-website-github-actions.yml)

6. That's it. Now you we verify your workflow is being succesfully run and that the objetcs are being uploaded into your amazon s3 bucket.


<p align="center">
  <img src="https://github.com/user-attachments/assets/731ceaa7-a686-4d2d-b3c4-0655510aa7ae" alt="Description of image" width="1200">
</p>


<p align="center">
  <img src="https://github.com/user-attachments/assets/c8a8a27f-ee2d-4118-9d25-74c9ad642366" alt="Description of image" width="1200">
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/637b55c9-44fd-473a-82ed-ac9c6d5dbd53" alt="Description of image" width="1200">
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/cf34f402-57e2-4953-abae-df118d612471" alt="Description of image" width="1200">
</p>



