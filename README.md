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
3. According to security best practices, configure IAM role to permit your github workflow to upload files into your newly created bucket without the need to store long term user credentials as secrets. Configure OpenID Connect (OIDC) for AWS to trust github's OIDC federated identity. For details see https://docs.github.com/en/actions/security-for-github-actions/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services
4. Using AWS IAM, create a new custom IAM role and configure the following trust policy. Replace placeholders <AWS_ACCOUNT_ID>, <REPO_OWNER>, <REPO_NAME> and <BRANCH_NAME> with your data. It's quite important to specify with condition statement who can assume the role using the github's OIDC.
~~~
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Federated": "arn:aws:iam::<AWS_ACCOUNT_ID>:oidc-provider/token.actions.githubusercontent.com"
            },
            "Action": "sts:AssumeRoleWithWebIdentity",
            "Condition": {
                "StringEquals": {
                    "token.actions.githubusercontent.com:aud": "sts.amazonaws.com",
                    "token.actions.githubusercontent.com:sub": "repo:<REPO_OWNER>/<REPO_NAME>:refs/heads/<REPO_BRANCH>"
                }
            }
        }
    ]
}
~~~
5. configure github workflow to upload static website files to s3 bucket on git push event (See code in .gitub/workflows/deploy-static-website-github-actions.yml)

And that's it. Now you can verify your workflow is being succesfully run and that the objetcs are being upload into your amazon s3 bucket.
